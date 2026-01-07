package auth

import (
	"net/http"

	"github.com/applybuy-pkg/pkg/middleware/auth/jwtauth"
	"github.com/applybuy-pkg/pkg/middleware/auth/machinetoken"
	"github.com/applybuy-token-service/internal/config"/pkg/v2/common/middlewares"
)

func Middleware(appContext *config.ApplicationConfig) middlewares.Middleware {
	return func(next http.Handler) http.Handler {
		return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {

			// Decide auth type based on header
			authType := r.Header.Get("X-Auth-Type")

			// JWT auth path
			if authType == "jwt" {
				jwtMiddleware := jwtauth.NewAuthMiddleware(
					jwtauth.ScopeConfig{
						AcceptedScopes: []string{}, // reviewer asked to keep empty
					},
					appContext.JwtFetcher(),
				)

				jwtMiddleware(next).ServeHTTP(w, r)
				return
			}

			// Default: Okta machine token
			oktaMiddleware := machinetoken.Middleware(
				machinetoken.Config{
					ForwardToNextMiddleware: false,
					RequiredScopes:          []string{}, // keep empty as instructed
				},
			)

			oktaMiddleware(next).ServeHTTP(w, r)
		})
	}
}