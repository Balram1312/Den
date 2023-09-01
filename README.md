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

-----
package yourpackage

import (
    "bytes"
    "net/http"
    "net/http/httptest"
    "testing"
    "github.com/gin-gonic/gin"
    "github.com/stretchr/testify/assert"
    // Import your database package or mock here
)

type MyReceiver struct {
    // Receiver fields
}

func (r *MyReceiver) PostHandler(c *gin.Context) {
    var requestBody struct {
        UserID   int    `json:"user_id"`
        Message  string `json:"message"`
    }
    if err := c.ShouldBindJSON(&requestBody); err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": "Invalid JSON"})
        return
    }
    
    // Insert into database using your database package or mock
    if err := InsertRecord(requestBody.UserID, requestBody.Message); err != nil {
        c.JSON(http.StatusInternalServerError, gin.H{"error": "Database error"})
        return
    }

    c.JSON(http.StatusOK, gin.H{"response": "Record inserted"})
}

func TestPostHandler(t *testing.T) {
    // Set up your router, mock database, etc.

    // Test case 1: Successful insertion
    validBody := `{"user_id": 1, "message": "Hello, server!"}`
    req1, _ := http.NewRequest("POST", "/post", bytes.NewBuffer([]byte(validBody)))
    req1.Header.Set("Content-Type", "application/json")
    w1 := httptest.NewRecorder()
    router.ServeHTTP(w1, req1)
    assert.Equal(t, http.StatusOK, w1.Code)
    assert.Contains(t, w1.Body.String(), "Record inserted")

    // Test case 2: Invalid JSON
    req2, _ := http.NewRequest("POST", "/post", bytes.NewBuffer([]byte("invalid json")))
    req2.Header.Set("Content-Type", "application/json")
    w2 := httptest.NewRecorder()
    router.ServeHTTP(w2, req2)
    assert.Equal(t, http.StatusBadRequest, w2.Code)
    assert.Contains(t, w2.Body.String(), "Invalid JSON")

    // Test case 3: Database error (invalid user_id)
    invalidUserIDBody := `{"user_id": 999, "message": "Hello, server!"}`
    req3, _ := http.NewRequest("POST", "/post", bytes.NewBuffer([]byte(invalidUserIDBody)))
    req3.Header.Set("Content-Type", "application/json")
    w3 := httptest.NewRecorder()
    router.ServeHTTP(w3, req3)
    assert.Equal(t, http.StatusInternalServerError, w3.Code)
    assert.Contains(t, w3.Body.String(), "Database error")
}

