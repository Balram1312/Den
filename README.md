# Den
package yourpackage

import (
    "net/http"
    "net/http/httptest"
    "testing"
    "github.com/gin-gonic/gin"
    "github.com/stretchr/testify/assert"
)

type MyReceiver struct {
    // Receiver fields
}

func (r *MyReceiver) MethodHandler(c *gin.Context) {
    c.String(http.StatusOK, "Hello from the method handler")
}

func TestMethodHandler(t *testing.T) {
    router := gin.Default()
    receiver := &MyReceiver{}
    
    router.GET("/route1", receiver.MethodHandler)

    req, _ := http.NewRequest("GET", "/route1", nil)
    w := httptest.NewRecorder()

    router.ServeHTTP(w, req)

    assert.Equal(t, http.StatusOK, w.Code)
    assert.Equal(t, "Hello from the method handler", w.Body.String())
}

-----


package yourpackage

import (
    "net/http"
    "net/http/httptest"
    "testing"
    "github.com/gin-gonic/gin"
)

type MyReceiver struct {
    // Receiver fields
}

func (r *MyReceiver) MethodHandler(c *gin.Context) {
    c.String(http.StatusOK, "Hello from the method handler")
}

func TestMethodHandler(t *testing.T) {
    router := gin.Default()
    receiver := &MyReceiver{} // Create an instance of the receiver
    
    // Use the method handler from the receiver
    router.GET("/route1", receiver.MethodHandler)

    req, _ := http.NewRequest("GET", "/route1", nil)
    w := httptest.NewRecorder()

    router.ServeHTTP(w, req)

    if w.Code != http.StatusOK {
        t.Errorf("Expected status %d; got %d", http.StatusOK, w.Code)
    }
    if w.Body.String() != "Hello from the method handler" {
        t.Errorf("Expected body %s; got %s", "Hello from the method handler", w.Body.String())
    }
}

