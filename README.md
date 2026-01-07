package auth

import (
	"net/http"

	jwtAuth "github.com/LatitudeFiial/auth"
	"github.com/Latitucial/applybuy-pkg/pkg/middleware/auth/machinetoken"
	"github.com/Latitucial/applybuy-token-service/internal/config"
	"github.com/Laial/pkg/v2/common/logger"
	"github.com/Latitudel/pkg/v2/common/middlewares"
)

func Middleware(
	appContext *config.ApplicationConfig,
	requiredScopes []string,
) middlewares.Middleware {

	return func(next http.Handler) http.Handler {
		return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {

			// If X-Auth-Type is set, use JWT auth
			if r.Header.Get("X-Auth-Type") != "" {

				authMiddleware := jwtAuth.NewAuthMiddleware(
					jwtAuth.ScopeConfig{
						AcceptedScopes: requiredScopes,
					},
					appContext.JwtFetcher(), // ✅ METHOD CALL (this is correct for token-service)
				)

				authMiddleware(next).ServeHTTP(w, r)
				return
			}

			// Default: Okta machine token
			oktaMiddleware := machinetoken.Middleware(
				machinetoken.Config{
					ForwardToNextMiddleware: false,
					RequiredScopes:          []string{}, // reviewer asked to keep empty
					OnError: func(err error) {
						logger.
							WithField("path", r.URL.Path).
							Infof("error from middleware: %v", err)
					},
				},
			)

			oktaMiddleware(next).ServeHTTP(w, r)
		})
	}
}