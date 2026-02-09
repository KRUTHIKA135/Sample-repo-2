| Date | Project Name | Service Name | Project Type | Task Assigned | Task Description | Files / Areas Touched | Tools / Technologies Used | What I Did | What I Learned | Outcome / Status | Remarks |
|------|--------------|--------------|--------------|---------------|------------------|-----------------------|---------------------------|------------|----------------|------------------|---------|
| (add date) | Student Management REST API | Student Service | Learning / Practice | Create REST API in Go | CRUD REST API using net/http with JSON storage | main.go, routes, handlers, models, JSON file | Go, JSON, VS Code | Built REST API with CRUD, structured code, used JSON file | Go REST basics, CRUD, JSON handling | Completed | Learning project |
| (add date) | Letter Service | letter-service | EOL Project | PR Review | Review Go upgrade from 1.21 to 1.24 | go.mod, Dockerfile, CI files | Go, GitHub, Docker | Reviewed PR, verified Go upgrade and lint fixes | PR review skills, upgrade impact | Reviewed | No logic changes |
| (add date) | Letter Service | letter-service | EOL Project | Architecture Understanding | Understand email flow and APIs | handlers, services, templates, swagger | Go, REST, Swagger | Studied flow, endpoints, templates | Email service architecture | Understood | Fresher focus |
| (add date) | Letter Service | letter-service | EOL Project | Lint Fixes | Understand lint changes post upgrade | config, email, otp, tests | Go, golangci-lint | Reviewed lint fixes | Lint importance | Understood | Non-functional |
| (add date) | Other Simple Service | — | Active | Context usage | Learn context propagation | handlers, services | Go | Studied context passing | Context lifecycle | Understood | Basics |
| (add date) | Letter Service | letter-service | EOL Project | Buildkite Intro | Understand CI pipeline | Buildkite configs | Buildkite, Go, Docker | Learned CI flow | CI/CD basics | Understood | Intro |
| (add date) | Apply & Buy | Token Service | Backend/Security | Machine token middleware | Machine token auth support | middleware, swagger, client | Go, OAuth2, OpenAPI | Added middleware, updated swagger | Auth + client gen | Completed | Aligned |
| (add date) | Apply & Buy | Payment Service | Backend/Security | Route updates | Align with machine token | routes, swagger, client | Go, OAuth2 | Updated endpoints | Cross-service auth | Completed | Consistent |
| (add date) | Apply & Buy | Merchant DDB Stream | Event Processing | Webhook auth | Bearer token auth | handlers, configs | Go, Okta, AWS | Updated auth flow | Okta + streams | Completed | Webhooks |
| (add date) | Apply & Buy | Worker Service | Async Processing | Webhook updates | Align webhook logic | structs, env files | Go, OAuth2 | Updated webhook flow | Env configs | Completed | Paired |
| (add date) | Apply & Buy | Checkout Service | Backend/API | Swagger + client | Regenerate client | swagger, client | OpenAPI, Go | Updated swagger, client | OpenAPI mastery | Completed | Tests pass |
| (add date) | Apply & Buy | Merchant Service | Backend/API | End-to-end setup | Middleware to client gen | middleware, routes | Go, OAuth2, OpenAPI | Full setup | End-to-end flow | Completed | Ownership | 



.........................................



openapi: "3.0.3"

info:
  title: Apply & Buy Checkout Service
  version: 1.0.0

servers:
  - url: https://api.example.com/v1

security:
  - oauth2: []

paths:

  ### 1) accounts
  /accounts/profile/by-email:
    get:
      summary: Get account profile by email
      operationId: getAccountProfileByEmail
      tags: [Accounts]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }

        - name: X-Auth-Type
          in: header
          required: false
          description: Authentication type (e.g., "jwt")
          schema: { type: string }

        - name: email
          in: query
          required: true
          schema:
            type: string
            format: email
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:accounts:read

  /accounts/profile/training-attended:
    post:
      summary: Update training attended
      operationId: updateAccountProfileTrainingAttended
      tags: [Accounts]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }

        - name: X-Auth-Type
          in: header
          required: false
          description: Authentication type (e.g., "jwt")
          schema: { type: string }
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TrainingAttendedUpdateRequest'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:accounts:write

  ### 2) admin
  /admin/merchant:
    post:
      summary: Create merchant
      operationId: adminCreateMerchant
      tags: [Admin]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateMerchantRequest'
      responses:
        "201":
          description: Created
      security:
        - oauth2:
            - applybuy-checkout-service:admin:write

  ### 3) cancel
  /cancel:
    post:
      summary: Cancel payment
      operationId: postCancel
      tags: [Payments, Cancel]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }

        - name: X-Auth-Type
          in: header
          required: false
          description: Authentication type (e.g., "jwt")
          schema: { type: string }
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CancelRequest'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:payments:write

  ### 4) capture
  /capture:
    post:
      summary: Capture payment
      operationId: postCapture
      tags: [Payments, Capture]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }

        - name: X-Auth-Type
          in: header
          required: false
          description: Authentication type (e.g., "jwt")
          schema: { type: string }
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CaptureRequest'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:payments:write

  ### 5) customer
  /customer/transactions:
    get:
      summary: Get customer transactions
      operationId: customerGetTransactions
      tags: [Customer, Transactions]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:customer:read

  ### 6) dev
  /dev/test-email:
    get:
      summary: Dev test email
      operationId: devTestEmail
      tags: [Dev]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:dev:read

  ### 7) health (PUBLIC)
  /health:
    get:
      summary: Health check
      operationId: healthCheck
      tags: [Health]
      security: []
      responses:
        "200":
          description: OK

  ### 8) merchant
  /merchant:
    get:
      summary: Get merchant
      operationId: getMerchant
      tags: [Merchant]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:merchant:read

  ### 9) order
  /order:
    get:
      summary: Get order
      operationId: getOrder
      tags: [Orders]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:orders:read

  ### 10) payment
  /payment/embed:
    get:
      summary: Payment embed
      operationId: paymentEmbed
      tags: [Payment]
      responses:
        "200":
          description: Success

  ### 11) private
  /private/merchants:
    get:
      summary: Private get merchants
      operationId: privateGetMerchants
      tags: [Private]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:private:read

  ### 12) products
  /products:
    post:
      summary: Create products
      operationId: createProducts
      tags: [Products]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ProductCreateRequest'
      responses:
        "201":
          description: Created
      security:
        - oauth2:
            - applybuy-checkout-service:products:write

  ### 13) promotions
  /promotions:
    get:
      summary: Get promotions
      operationId: getMerchantPromotions
      tags: [Promotions]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:promotions:read

  ### 14) purchase
  /purchase:
    post:
      summary: Create purchase
      operationId: purchaseCreate
      tags: [Purchase]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PurchaseRequest'
      responses:
        "201":
          description: Created
      security:
        - oauth2:
            - applybuy-checkout-service:purchase:write

  ### 15) redirect (PUBLIC)
  /redirect:
    get:
      summary: Redirect info
      operationId: redirectInfo
      tags: [Redirect]
      security: []
      responses:
        "200":
          description: Success

  ### 16) refund
  /refund:
    post:
      summary: Create refund
      operationId: postRefund
      tags: [Refund]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }

        - name: X-Auth-Type
          in: header
          required: false
          description: Authentication type (e.g., "jwt")
          schema: { type: string }
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/RefundRequest'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:payments:write

  ### 17) shopify
  /shopify/authorize:
    get:
      summary: Shopify authorize
      operationId: shopifyAuthorizeGet
      tags: [Shopify]
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:shopify:read

  ### 18) transaction
  /transactions:
    get:
      summary: List transactions
      operationId: transactionList
      tags: [Transactions]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:transactions:read

  ### 19) void
  /void:
    post:
      summary: Void payment
      operationId: postVoid
      tags: [Void]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/VoidRequest'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:payments:write

  ### 20) webhookManagement
  /webhook/management:
    post:
      summary: Webhook management
      operationId: webhookOrderManagement
      tags: [Webhook]
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          description: Correlation ID for tracing requests across services
          schema: { type: string }
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/WebhookOrderManagementRequest'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:webhook:write

components:
  schemas:
    ProfileListResponse: { type: object }
    TrainingAttendedUpdateRequest: { type: object }
    CreateMerchantRequest: { type: object }
    CancelRequest: { type: object }
    CaptureRequest: { type: object }
    ProductCreateRequest: { type: object }
    PurchaseRequest: { type: object }
    RefundRequest: { type: object }
    VoidRequest: { type: object }
    WebhookOrderManagementRequest: { type: object }

  securitySchemes:
    oauth2:
      type: oauth2
      flows:
        clientCredentials:
          tokenUrl: https://api.example.com/oauth2/token
          scopes:
            applybuy-checkout-service:accounts:read: Accounts read
            applybuy-checkout-service:accounts:write: Accounts write
            applybuy-checkout-service:admin:write: Admin write
            applybuy-checkout-service:payments:read: Payments read
            applybuy-checkout-service:payments:write: Payments write
            applybuy-checkout-service:transactions:read: Transactions read
            applybuy-checkout-service:merchant:read: Merchant read
            applybuy-checkout-service:orders:read: Orders read
            applybuy-checkout-service:products:write: Products write
            applybuy-checkout-service:promotions:read: Promotions read
            applybuy-checkout-service:purchase:write: Purchase write
            applybuy-checkout-service:shopify:read: Shopify read
            applybuy-checkout-service:webhook:write: Webhook write















.............
openapi: "3.0.3"

info:
  title: Apply & Buy Checkout Service
  version: 1.0.0

servers:
  - url: https://api.example.com/v1

security:
  - oauth2: []

paths:

  ### 1) accounts
  /accounts/profile/by-email:
    get:
      summary: Get account profile by email
      operationId: getAccountProfileByEmail
      tags: [Accounts]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
        - name: email
          in: query
          required: true
          schema:
            type: string
            format: email
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ProfileListResponse'
      security:
        - oauth2:
            - applybuy-checkout-service:accounts:read

  /accounts/profile/training-attended:
    post:
      summary: Update training attended
      operationId: updateAccountProfileTrainingAttended
      tags: [Accounts]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TrainingAttendedUpdateRequest'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:accounts:write

  ### 2) admin
  /admin/merchant:
    post:
      summary: Create merchant
      operationId: adminCreateMerchant
      tags: [Admin]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateMerchantRequest'
      responses:
        "201":
          description: Created
      security:
        - oauth2:
            - applybuy-checkout-service:admin:write

  ### 3) cancel
  /cancel:
    post:
      summary: Cancel payment
      operationId: postCancel
      tags: [Payments, Cancel]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CancelRequest'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:payments:write

  ### 4) capture
  /capture:
    post:
      summary: Capture payment
      operationId: postCapture
      tags: [Payments, Capture]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CaptureRequest'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:payments:write

  ### 5) customer
  /customer/transactions:
    get:
      summary: Get customer transactions
      operationId: customerGetTransactions
      tags: [Customer, Transactions]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:customer:read

  ### 6) dev
  /dev/test-email:
    get:
      summary: Dev test email
      operationId: devTestEmail
      tags: [Dev]
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:dev:read

  ### 7) health
  /health:
    get:
      summary: Health check
      operationId: healthCheck
      tags: [Health]
      security: []
      responses:
        "200":
          description: OK

  ### 8) merchant
  /merchant:
    get:
      summary: Get merchant
      operationId: getMerchant
      tags: [Merchant]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:merchant:read

  ### 9) order
  /order:
    get:
      summary: Get order
      operationId: getOrder
      tags: [Orders]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:orders:read

  ### 10) payment
  /payment/embed:
    get:
      summary: Payment embed
      operationId: paymentEmbed
      tags: [Payment]
      responses:
        "200":
          description: Success

  ### 11) private
  /private/merchants:
    get:
      summary: Private get merchants
      operationId: privateGetMerchants
      tags: [Private]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:private:read

  ### 12) products
  /products:
    post:
      summary: Create products
      operationId: createProducts
      tags: [Products]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ProductCreateRequest'
      responses:
        "201":
          description: Created
      security:
        - oauth2:
            - applybuy-checkout-service:products:write

  ### 13) promotions
  /promotions:
    get:
      summary: Get promotions
      operationId: getMerchantPromotions
      tags: [Promotions]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:promotions:read

  ### 14) purchase
  /purchase:
    post:
      summary: Create purchase
      operationId: purchaseCreate
      tags: [Purchase]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PurchaseRequest'
      responses:
        "201":
          description: Created
      security:
        - oauth2:
            - applybuy-checkout-service:purchase:write

  ### 15) redirect
  /redirect:
    get:
      summary: Redirect info
      operationId: redirectInfo
      tags: [Redirect]
      security: []
      responses:
        "200":
          description: Success

  ### 16) refund
  /refund:
    post:
      summary: Create refund
      operationId: postRefund
      tags: [Refund]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/RefundRequest'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:payments:write

  ### 17) shopify
  /shopify/authorize:
    get:
      summary: Shopify authorize
      operationId: shopifyAuthorizeGet
      tags: [Shopify]
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:shopify:read

  ### 18) transaction
  /transactions:
    get:
      summary: List transactions
      operationId: transactionList
      tags: [Transactions]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:transactions:read

  ### 19) void
  /void:
    post:
      summary: Void payment
      operationId: postVoid
      tags: [Void]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/VoidRequest'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:payments:write

  ### 20) webhookManagement
  /webhook/management:
    post:
      summary: Webhook management
      operationId: webhookOrderManagement
      tags: [Webhook]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/WebhookOrderManagementRequest'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-checkout-service:webhook:write

components:

  parameters:
    XCorrelationId:
      name: X-Correlation-Id
      in: header
      required: false
      schema:
        type: string

    XAuthType:
      name: X-Auth-Type
      in: header
      required: false
      schema:
        type: string

  schemas:
    ProfileListResponse: { type: object }
    TrainingAttendedUpdateRequest: { type: object }
    CreateMerchantRequest: { type: object }
    CancelRequest: { type: object }
    CaptureRequest: { type: object }
    ProductCreateRequest: { type: object }
    PurchaseRequest: { type: object }
    RefundRequest: { type: object }
    VoidRequest: { type: object }
    WebhookOrderManagementRequest: { type: object }

  securitySchemes:
    oauth2:
      type: oauth2
      flows:
        clientCredentials:
          tokenUrl: https://api.example.com/oauth2/token
          scopes:
            applybuy-checkout-service:accounts:read: Accounts read
            applybuy-checkout-service:accounts:write: Accounts write
            applybuy-checkout-service:admin:write: Admin write
            applybuy-checkout-service:payments:read: Payments read
            applybuy-checkout-service:payments:write: Payments write
            applybuy-checkout-service:transactions:read: Transactions read
            applybuy-checkout-service:merchant:read: Merchant read
            applybuy-checkout-service:orders:read: Orders read
            applybuy-checkout-service:products:write: Products write
            applybuy-checkout-service:promotions:read: Promotions read
            applybuy-checkout-service:purchase:write: Purchase write
            applybuy-checkout-service:shopify:read: Shopify read
            applybuy-checkout-service:webhook:write: Webhook write
















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
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
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
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
        - name: transactionId
          in: path
          required: true
          schema:
            type: string
        - name: refundId
          in: path
          required: true
          schema:
            type: string
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
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
        - name: transactionId
          in: path
          required: true
          schema:
            type: string
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
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
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
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
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
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
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
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
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

  /transactions:
    get:
      summary: List transactions by merchant
      operationId: transactionListByMerchant
      tags: [Transactions]
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
        - name: merchantId
          in: query
          required: true
          schema:
            type: string
        - name: from
          in: query
          schema:
            type: string
            format: date-time
        - name: to
          in: query
          schema:
            type: string
            format: date-time
        - name: page
          in: query
          schema:
            type: integer
        - name: limit
          in: query
          schema:
            type: integer
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
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
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
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
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

  parameters:
    XCorrelationId:
      name: X-Correlation-Id
      in: header
      required: false
      schema:
        type: string

    XAuthType:
      name: X-Auth-Type
      in: header
      required: false
      schema:
        type: string

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