authCfg := clientcredentials.Config{
    ClientID:     container.EnvironmentConstants.OKTAClientID,
    ClientSecret: container.EnvironmentConstants.OKTAClientSecret,
    TokenURL: fmt.Sprintf(
        "%s/%s",
        container.EnvironmentConstants.OKTAIssuer,
        core.OKTATokenPath,
    ),
}