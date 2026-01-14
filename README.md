openapi: "3.0.3"

info:
  title: Apply & Buy Checkout Service
  version: 1.0.0

servers:
  - url: https://api.example.com/v1

security:
  - oauth2: []

paths:

  /redirect:
    get:
      summary: Redirect info
      operationId: redirectInfo
      tags: [Redirect]
      security: []   # public redirect
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/RedirectInfoResponse'
        "500":
          description: Error

  /refund:
    post:
      summary: Create refund
      operationId: postRefund
      tags: [Refund, Payments]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/RefundRequest'
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/RefundResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:payments:write

  /refund/{transactionId}/{refundId}:
    get:
      summary: Get refund by refund ID
      operationId: getRefundByRefundID
      tags: [Refund, Payments]
      parameters:
        - name: transactionId
          in: path
          required: true
          schema: { type: string }
        - name: refundId
          in: path
          required: true
          schema: { type: string }
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/RefundResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:payments:read

  /refund/{transactionId}:
    get:
      summary: Get refunds by transaction
      operationId: getRefundsByTransaction
      tags: [Refund, Payments]
      parameters:
        - name: transactionId
          in: path
          required: true
          schema: { type: string }
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/RefundListResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:payments:read

  /shopify/authorize:
    get:
      summary: Shopify authorize (GET)
      operationId: shopifyAuthorizeGet
      tags: [Shopify]
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ShopifyAuthorizeResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:shopify:read

    post:
      summary: Shopify authorize (POST)
      operationId: shopifyAuthorizePost
      tags: [Shopify]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ShopifyAuthorizeRequest'
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ShopifyAuthorizeResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:shopify:write

  /shopify/token:
    get:
      summary: Get Shopify token
      operationId: shopifyTokenGet
      tags: [Shopify]
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ShopifyTokenResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:shopify:read

  /shopify/shop/erase:
    post:
      summary: GDPR shop erase
      operationId: shopifyShopDataErase
      tags: [Shopify, GDPR]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/GDPRShopEraseRequest'
      responses:
        "202":
          description: Accepted
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/GDPRShopEraseResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:gdpr:write

  /shopify/customer/erase:
    post:
      summary: GDPR customer erase
      operationId: shopifyCustomerDataErase
      tags: [Shopify, GDPR]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/GDPRCustomerEraseRequest'
      responses:
        "202":
          description: Accepted
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/GDPRCustomerEraseResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:gdpr:write

  /shopify/customer/read:
    post:
      summary: GDPR customer read
      operationId: shopifyCustomerDataRead
      tags: [Shopify, GDPR]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/GDPRCustomerReadRequest'
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/GDPRCustomerReadResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:gdpr:read

  /transactions/search:
    get:
      summary: Search transaction summaries
      operationId: transactionSummarySearch
      tags: [Transactions, Summary]
      parameters:
        - name: q
          in: query
          schema: { type: string }
        - name: page
          in: query
          schema: { type: integer }
        - name: limit
          in: query
          schema: { type: integer }
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionSummarySearchResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:transactions:read

  /transactions/{transactionId}:
    get:
      summary: Get transaction summary by ID
      operationId: transactionSummaryGetByTransactionID
      tags: [Transactions, Summary]
      parameters:
        - name: transactionId
          in: path
          required: true
          schema: { type: string }
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionSummaryResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:transactions:read

  /transactions:
    get:
      summary: List transactions by merchant
      operationId: transactionListByMerchant
      tags: [Transactions]
      parameters:
        - name: merchantId
          in: query
          required: true
          schema: { type: string }
        - name: from
          in: query
          schema: { type: string, format: date-time }
        - name: to
          in: query
          schema: { type: string, format: date-time }
        - name: page
          in: query
          schema: { type: integer }
        - name: limit
          in: query
          schema: { type: integer }
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionsListResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:transactions:read

  /void:
    post:
      summary: Void payment
      operationId: postVoid
      tags: [Payments, Void]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/VoidRequest'
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/VoidResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:payments:write

  /webhook/management:
    post:
      summary: Webhook order management
      operationId: webhookOrderManagement
      tags: [Webhook, Management]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/WebhookOrderManagementRequest'
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/WebhookOrderManagementResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:webhook:write

components:

  schemas:
    RedirectInfoResponse: { type: object }

    RefundRequest: { type: object }
    RefundResponse: { type: object }
    RefundListResponse: { type: object }

    ShopifyAuthorizeRequest: { type: object }
    ShopifyAuthorizeResponse: { type: object }
    ShopifyTokenResponse: { type: object }

    GDPRShopEraseRequest: { type: object }
    GDPRShopEraseResponse: { type: object }
    GDPRCustomerEraseRequest: { type: object }
    GDPRCustomerEraseResponse: { type: object }
    GDPRCustomerReadRequest: { type: object }
    GDPRCustomerReadResponse: { type: object }

    TransactionSummarySearchResponse: { type: object }
    TransactionSummaryResponse: { type: object }
    TransactionsListResponse: { type: object }

    VoidRequest: { type: object }
    VoidResponse: { type: object }

    WebhookOrderManagementRequest: { type: object }
    WebhookOrderManagementResponse: { type: object }

  securitySchemes:
    oauth2:
      type: oauth2
      flows:
        clientCredentials:
          tokenUrl: https://api.example.com/oauth2/token
          scopes:
            applybuy-checkout-service:payments:read: Read payments
            applybuy-checkout-service:payments:write: Modify payments
            applybuy-checkout-service:transactions:read: Read transactions
            applybuy-checkout-service:shopify:read: Read Shopify data
            applybuy-checkout-service:shopify:write: Modify Shopify auth
            applybuy-checkout-service:gdpr:read: Read GDPR data
            applybuy-checkout-service:gdpr:write: Modify GDPR data
            applybuy-checkout-service:webhook:write: Webhook management