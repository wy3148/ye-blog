---
title: Use Golang to develop web service--gorilla/mux 
date: 2019-10-20 21:58:05
tags: golang
categories: golang
---

This is simple notes about using opensource gorilla/mux to implement API service

#Define the router

```
	router := mux.NewRouter()
	
	//try to get a product by its unique id
	router.HandleFunc("/product/{id}", getProduct).Methods("GET")
	
	//post a new product, POST method is allowed only
	router.HandleFunc("/product", newProduct).Methods("POST")
	
	//a more general api example
	router.HandleFunc("/api/health", func(w http.ResponseWriter, r *http.Request) {
		// an example API handler
		json.NewEncoder(w).Encode(map[string]bool{"ok": true})
	})
	
```


```Go


func getProduct(w http.ResponseWriter, r *http.Request) {

	//this is about how to get router variables
	//in this example it is 'id' 
	//vars['id']
	vars := mux.Vars(r)
	w.WriteHeader(http.StatusOK)
	fmt.Println(vars)
	
	//extra variabes /product/1/filter=last5mins
	r.ParseForm()
	filterRule := r.FormValue("filter")
	fmt.Println(filterRule)
}

type Product struct {
	Name   string
	Price  int
	Vendor string
}

func newProduct(w http.ResponseWriter, r *http.Request) {
	var p Product
	
	//this is about parse the POST body request into a 
	//golang struct 
	if err := json.NewDecoder(r.Body).Decode(&p); err != nil {
		w.WriteHeader(http.StatusBadRequest)
		w.Write([]byte(err.Error()))
		return
	}
	fmt.Println(p.Name, p.Price)
	w.WriteHeader(http.StatusOK)
	w.Write([]byte("ok"))
}

```


#use a subrouter

we can define a prefix sub router
```
	s := router.PathPrefix("/clothes").Subrouter()

	//http: //localhost:8000/clothes/
	s.HandleFunc("/", listClothes)

	//http://localhost:8000/clothes/1123
	s.HandleFunc("/{id}", getClothes)

	//http://localhost:8000/clothes/1123/details

	s.HandleFunc("/{id}/details", getClothesDetails)
```



#use middleware 


Here is Autentication middleware, if authorization fails, return error

```
// Define our struct
type authenticationMiddleware struct {
	tokenUsers map[string]string
}

// Initialize it somewhere
func (amw *authenticationMiddleware) Populate() {
	amw.tokenUsers["00000000"] = "user0"
	amw.tokenUsers["aaaaaaaa"] = "userA"
	amw.tokenUsers["05f717e5"] = "randomUser"
	amw.tokenUsers["deadbeef"] = "user0"
}

// Middleware function, which will be called for each request
func (amw *authenticationMiddleware) Middleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        token := r.Header.Get("X-Session-Token")

        if user, found := amw.tokenUsers[token]; found {
        	// We found the token in our map
        	log.Printf("Authenticated user %s\n", user)
        	// Pass down the request to the next middleware (or final handler)
        	next.ServeHTTP(w, r)
        } else {
        	// Write an error and stop the handler chain
        	http.Error(w, "Forbidden", http.StatusForbidden)
        }
    })
}
```

it support multiple middlewares


```

func loggingMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		// Do stuff here
		log.Println("middleware is called:", r.RequestURI)
		// Call the next handler, which can be another middleware in the chain, or the final handler.
		next.ServeHTTP(w, r)
	})
}

	router := mux.NewRouter()
	router.Use(loggingMiddleware)
	amw := authenticationMiddleware{}
	amw.tokenUsers = make(map[string]string)
	amw.Populate()
	router.Use(amw.Middleware)
```

