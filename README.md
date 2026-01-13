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