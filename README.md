# package main

import (
    "fmt"
    "io"
    "net/http"
    "os"
)

func uploadFile(w http.ResponseWriter, r *http.Request) {
    // Parse input file
    file, handler, err := r.FormFile("file")
    if err != nil {
        fmt.Println("Error Retrieving the File")
        fmt.Println(err)
        return
    }
    defer file.Close()

    // Create output file
    f, err := os.OpenFile(handler.Filename, os.O_WRONLY|os.O_CREATE, 0666)
    if err != nil {
        fmt.Println(err)
        return
    }
    defer f.Close()

    // Copy input file to output file
    io.Copy(f, file)

    // Send response
    fmt.Fprintf(w, "Successfully Uploaded File\n")
}

func main() {
    http.HandleFunc("/upload", uploadFile)

    fmt.Println("Server started on :8080")
    err := http.ListenAndServeTLS(":8080", "server.crt", "server.key", nil)
    if err != nil {
        fmt.Println("ListenAndServeTLS: ", err)
    }
}
