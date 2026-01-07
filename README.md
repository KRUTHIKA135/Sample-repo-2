func AuthMiddleware(
	appConfig *config.ApplicationConfig,
	requiredScopes []string, // passed from routes
) middlewares.Middleware {

	return func(next http.Handler) http.Handler {
		return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {

			var authMiddleware middlewares.Middleware

			if r.Header.Get("X-Auth-Type") != "" {
				// JWT path (Cognito)
				authMiddleware = jwtauth.NewAuthMiddleware(
					appConfig.JwtFetcher(),
					jwtauth.ScopeConfig{
						AcceptedScopes: requiredScopes,
					},
				)
			} else {
				// Okta fallback (scope-agnostic here)
				authMiddleware = machinetoken.Middleware(
					machinetoken.Config{
						ForwardToNextMiddleware: false,
						RequiredScopes:          nil,
						OnError: func(err error) {
							logger.WithField("path", r.URL.Path).
								Infof("error from middleware: %v", err)
						},
					},
				)
			}

			authMiddleware(next).ServeHTTP(w, r)
		})
	}
}





auth.ContextAwareAuthMiddleware(
	container,
	[]string{okta.TokenScopePaymentWrite},
)


auth.ContextAwareAuthMiddleware(
	container,
	[]string{okta.TokenScopeProfileRead},
)




Middlewares: []middlewares.Middleware{
	auth.ContextAwareAuthMiddleware(
		container,
		[]string{okta.TokenScopePaymentWrite},
	),
},