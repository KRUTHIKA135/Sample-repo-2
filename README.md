middleware.Auth(
	middleware.JWT(container), // EXISTING JWT/JWK middleware
	middleware.AuthConfig{
		OktaConfig: machinetoken.Config{
			ForwardToNextMiddleware: false,
			RequiredScopes: []string{okta.TokenScopeProfileRead},
			OnError: func(err error) {
				logger.WithField("path", Path).
					Infof("error from middleware: %v", err)
			},
		},
	},
)