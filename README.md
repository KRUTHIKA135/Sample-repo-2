package auth

import (
	"net/http"
	"strings"

	"github.com/pkg/v2/common/middlewares"
)

func Middleware() middlewares.Middleware {
	return func(next http.Handler) http.Handler {
		return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {

			authHeader := r.Header.Get("Authorization")
			if authHeader == "" {
				http.Error(w, "missing authorization header", http.StatusUnauthorized)
				return
			}

			if !strings.HasPrefix(authHeader, "Bearer ") {
				http.Error(w, "invalid authorization header", http.StatusUnauthorized)
				return
			}

			// DO NOT validate, DO NOT verify, DO NOT decode
			// Just pass request forward
			next.ServeHTTP(w, r)
		})
	}
}







