package auth

import (
	"net/http"
	"strings"

	"github.com/LatitudeFinancial/pkg/v2/common/middlewares"
)

func Middleware() middlewares.Middleware {
	return func(next http.Handler) http.Handler {
		return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {

			authHeader := r.Header.Get("Authorization")
			if authHeader == "" {
				http.Error(w, "missing authorization header", http.StatusUnauthorized)
				return
			}

			// JWT (Cognito / future)
			if strings.HasPrefix(authHeader, "Bearer ") {
				next.ServeHTTP(w, r)
				return
			}

			// Okta machine token (current)
			if strings.HasPrefix(authHeader, "SSWS ") {
				next.ServeHTTP(w, r)
				return
			}

			http.Error(w, "unsupported authorization type", http.StatusUnauthorized)
		})
	}
}





