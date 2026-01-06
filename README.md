package middleware

import (
	"net/http"

	"github/applybuy-pkg/pkg/middleware/auth/machinetoken"
	"github/pkg/v2/common/middlewares"
)

type AuthConfig struct {
	OktaConfig machinetoken.Config
}

func Auth(
	jwtMiddleware middlewares.Middleware,
	cfg AuthConfig,
) middlewares.Middleware {

	oktaMiddleware := machinetoken.Middleware(cfg.OktaConfig)

	return func(next http.Handler) http.Handler {
		return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {

			// Try JWT first
			jwtPassed := false

			jwtMiddleware(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
				jwtPassed = true
				next.ServeHTTP(w, r)
			})).ServeHTTP(w, r)

			if jwtPassed {
				return
			}

			// If JWT failed, try Okta
			oktaMiddleware(next).ServeHTTP(w, r)
		})
	}
}

