openapi: "3.0.3"

info:
  title: Apply & Buy Token Service
  version: "1.0.0"

servers:
  - url: https://api.example.com/v1

paths:
  /delegate:
    get:
      summary: Get delegate
      operationId: getDelegate
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-token-service:profile:read

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
      security:
        - oauth2:
            - applybuy-token-service:profile:write

    delete:
      summary: Delete delegate
      operationId: deleteDelegate
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-token-service:profile:write

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
      security:
        - oauth2:
            - applybuy-token-service:profile:read

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
      security:
        - oauth2:
            - applybuy-token-service:profile:write

    delete:
      summary: Delete profile
      operationId: deleteProfile
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-token-service:profile:write

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
      security:
        - oauth2:
            - applybuy-token-service:profile:read

  /profile/list:
    post:
      summary: List profiles
      operationId: listProfiles
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-token-service:profile:read

  /profile/state:
    put:
      summary: Update profile state
      operationId: updateProfileState
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-token-service:profile:read

  /purchase:
    post:
      summary: Create purchase
      operationId: createPurchase
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-token-service:payment.write

  /refund:
    post:
      summary: Refund payment
      operationId: refundPayment
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-token-service:payment.write

  /void:
    post:
      summary: Void payment
      operationId: voidPayment
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-token-service:payment.write

  /capture:
    post:
      summary: Capture payment
      operationId: capturePayment
      parameters:
        - $ref: '#/components/parameters/XCorrelationId'
        - $ref: '#/components/parameters/XAuthType'
      responses:
        "200":
          description: Success
      security:
        - oauth2:
            - applybuy-token-service:payment.write

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

  securitySchemes:
    oauth2:
      type: oauth2
      flows:
        clientCredentials:
          tokenUrl: https://api.example.com/oauth2/token
          scopes:
            applybuy-token-service:profile:read: Read profile data
            applybuy-token-service:profile:write: Write profile data
            applybuy-token-service:payment.write: Write payment data