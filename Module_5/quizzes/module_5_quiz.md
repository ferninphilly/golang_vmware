# Quiz for module 5

1. So we've started creating our application- please set up the api package. Please use the JSON unmarshal package. I have a copy of this locally but to actually get practice at doing this it's important that you guys do this part yourselves. The package should read from the api using our api key in the toml. [Here](http://www.omdbapi.com/) is the api we'll be using and here is the struct:

```go
type FilmResponse struct {
    Title    string
    Year     string
    Rated    string
    Released string
    Runtime  string
    Genre    string
    Director string
    Writer   string
    Actors   string
    Plot     string
    Language string
    Country  string
    Awards   string
    Poster   string
    Response string
}
```