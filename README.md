import (
    "context"
    "fmt"
    "strings"

    "github.com/aws/aws-lambda-go/events"
    "github.com/aws/aws-sdk-go/service/ssm"

    "github.com/LatitudeFinancial/applybuy-merchant-ddb-stream/internal/core"
    "github.com/LatitudeFinancial/applybuy-merchant-ddb-stream/pkg/logger"
    "github.com/LatitudeFinancial/applybuy-payment-service/pkg/adminclient"

    commonlogger "github.com/LatitudeFinancial/pkg/v2/common/logger"
)



resp, callErr := paymentClient.PostAdminTransactionOverview(
    ctx,
    &txParams,
    payload,
)

if callErr != nil || resp == nil || resp.StatusCode >= 400 {
    if resp != nil && resp.Body != nil {
        resp.Body.Close()
    }
    log.Errorf(
        "failed to send transaction overview to payment service: %v",
        callErr,
    )
    continue
}

if resp.Body != nil {
    resp.Body.Close()
}

log.Infof(
    "successfully upserted transaction details for merchant id [%s] and ref [%s]",
    payload.MerchantId,
    payload.Ref,
)


func main() {
    container, err := core.NewContainer()
    if err != nil {
        panic(fmt.Errorf("unable to load config, %w", err))
    }

    awsSession := session.Must(session.NewSession())
    ssmClient := ssm.New(awsSession)

    paymentClient := newPaymentServiceHTTPClient(context.Background(), container)

    lambda.Start(Handler(container, ssmClient, paymentClient))
}




