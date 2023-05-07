Gerne zeige ich Ihnen, wie Sie einen File-Uploader in Go schreiben können, der HTTPS nutzt und das File als Argument annimmt. Hier ist ein Beispielcode, den Sie als Ausgangspunkt verwenden können:

```go
package main

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
```

Dieser Code verwendet die `http`-Bibliothek von Go, um einen HTTPS-Server zu erstellen, der auf Port 8080 lauscht. Wenn Sie das Programm ausführen, sollten Sie in der Lage sein, auf https://localhost:8080/upload zuzugreifen, um eine Datei hochzuladen.

Beachten Sie, dass Sie für dieses Beispiel auch ein selbst signiertes TLS-Zertifikat benötigen, das von Ihrem Webbrowser akzeptiert wird. In diesem Beispiel wird ein Zertifikat mit den Dateien "server.crt" und "server.key" verwendet. Sie sollten jedoch ein echtes Zertifikat von einer vertrauenswürdigen Zertifizierungsstelle erwerben, wenn Sie planen, den Server im Produktionsbetrieb zu nutzen.
