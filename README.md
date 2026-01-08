openapi: "3.0.3"
info:
  title: Apply & Buy Token Service
  version: 1.0.0

servers:
  - url: https://api.example.com/v1

paths:
  /delegate:
    get:
      summary: Get delegate
      operationId: getDelegate
      parameters:
        - name: X-Correlation-Id
          in: header
          required: false
          schema:
            type: string
        - name: X-Auth-Type
          in: header
          required: false
          schema:
            type: string
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/DelegateResponse'
      security:
        - oauth2: []

    post:
      summary: Create delegate
      operationId: createDelegate
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/DelegateRequest'
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/DelegateResponse'
      security:
        - oauth2: []

    delete:
      summary: Delete delegate
      operationId: deleteDelegate
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/MessageResponse'
      security:
        - oauth2: []

  /profile:
    get:
      summary: Get profile
      operationId: getProfile
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ProfileResponse'
      security:
        - oauth2: []

    post:
      summary: Create profile
      operationId: createProfile
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ProfileRequest'
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ProfileResponse'
      security:
        - oauth2: []

    delete:
      summary: Delete profile
      operationId: deleteProfile
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/MessageResponse'
      security:
        - oauth2: []

  /profile/validate:
    post:
      summary: Validate profile
      operationId: validateProfile
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ValidationResponse'
      security:
        - oauth2: []

  /purchase:
    post:
      summary: Create purchase
      operationId: createPurchase
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PurchaseRequest'
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PaymentResponse'
      security:
        - oauth2: []

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
    MessageResponse:
      type: object
      properties:
        message:
          type: string
      required: [message]

    DelegateRequest:
      type: object
      properties:
        delegateId:
          type: string
      required: [delegateId]

    DelegateResponse:
      type: object
      properties:
        result:
          type: string
      required: [result]

    ProfileRequest:
      type: object
      properties:
        profileId:
          type: string
      required: [profileId]

    ProfileResponse:
      type: object
      properties:
        result:
          type: string
      required: [result]

    ValidationResponse:
      type: object
      properties:
        valid:
          type: boolean
      required: [valid]

    PurchaseRequest:
      type: object
      properties:
        amount:
          type: integer
        currency:
          type: string
      required: [amount, currency]

    PaymentResponse:
      type: object
      properties:
        status:
          type: string
      required: [status]

  securitySchemes:
    oauth2:
      type: oauth2
      flows:
        implicit:
          authorizationUrl: https://api.example.com/oauth2/token
          scopes: {}