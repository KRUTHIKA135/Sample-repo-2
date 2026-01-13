module github.com/LatitudeFinancial/applybuy-merchant-ddb-stream

go 1.22

require (
	github.com/LatitudeFinancial/env v1.1.1
	github.com/LatitudeFinancial/pkg/v2 v2.33.22

	github.com/aws/aws-lambda-go v1.40.0

	// AWS SDK v1 (used directly)
	github.com/aws/aws-sdk-go v1.44.156

	// AWS SDK v2 (used directly)
	github.com/aws/aws-sdk-go-v2/config v1.18.4
	github.com/aws/aws-sdk-go-v2/service/sns v1.20.9
	github.com/aws/aws-sdk-go-v2/service/ssm v1.33.2
)

require (
	github.com/aws/aws-sdk-go-v2 v1.18.0 // indirect
	github.com/aws/aws-sdk-go-v2/credentials v1.13.4 // indirect
	github.com/aws/aws-sdk-go-v2/feature/ec2/imds v1.12.20 // indirect
	github.com/aws/aws-sdk-go-v2/internal/configsources v1.1.33 // indirect
	github.com/aws/aws-sdk-go-v2/internal/endpoints/v2 v2.4.27 // indirect
	github.com/aws/aws-sdk-go-v2/internal/ini v1.3.27 // indirect
	github.com/aws/aws-sdk-go-v2/service/internal/presigned-url v1.9.20 // indirect
	github.com/aws/aws-sdk-go-v2/service/secretsmanager v1.16.9 // indirect
	github.com/aws/aws-sdk-go-v2/service/sso v1.11.26 // indirect
	github.com/aws/aws-sdk-go-v2/service/ssooidc v1.13.9 // indirect
	github.com/aws/aws-sdk-go-v2/service/sts v1.17.6 // indirect
	github.com/aws/smithy-go v1.13.5 // indirect

	github.com/caarlos0/env/v6 v6.10.4 // indirect
	github.com/jmespath/go-jmespath v0.4.0 // indirect
	github.com/sirupsen/logrus v1.9.0 // indirect
	golang.org/x/sys v0.3.0 // indirect
	golang.org/x/xerrors v0.0.0-20220907171357-04be3eba64a2 // indirect
)