# Requirements Document: NiryatSetu

## Introduction

NiryatSetu (The Bridge to Exports) is an AI-powered export management platform designed to empower 63 million Indian Micro, Small, and Medium Enterprises (MSMEs) to participate in global trade. The platform addresses the critical barriers that prevent MSMEs from exporting: complex HS Code classification, unknown global pricing dynamics, documentation errors, and language barriers in international negotiations.

The system replaces expensive export consultants with a Multi-Agent System powered by Amazon Bedrock, providing a "Zero-Touch" Agentic Export Manager that automates the entire "Local-to-Global" journey. This solution was developed as a winning entry for the 'AI for Bharat' Hackathon in the Professional Track: AI for Retail, Commerce & Market Intelligence.

## Glossary

- **MSME**: Micro, Small, and Medium Enterprise - businesses with investment up to ₹50 crore and turnover up to ₹250 crore
- **HS_Code**: Harmonized System Code - a standardized 6-8 digit product classification system used globally for customs and trade
- **FOB_Price**: Free On Board Price - the cost of goods including delivery to the port of shipment
- **Compliance_Agent**: Amazon Bedrock Agent responsible for HS Code classification and duty calculation
- **Market_Intelligence_Agent**: Amazon Bedrock Agent responsible for pricing analysis and recommendations
- **Documentation_Agent**: Amazon Bedrock Agent responsible for generating export documents
- **Negotiation_Bot**: WhatsApp-integrated multilingual chatbot for buyer-seller communication
- **Orchestrator**: AWS Step Functions workflow coordinating multi-agent interactions
- **Knowledge_Base**: Amazon Bedrock Knowledge Base with RAG capabilities containing DGFT and tariff data
- **DGFT**: Directorate General of Foreign Trade - Indian government body regulating foreign trade
- **Guardrails**: Amazon Bedrock Guardrails - safety mechanisms preventing hallucinated legal advice
- **Deal_Flow**: The complete lifecycle of an export transaction from inquiry to completion
- **Platform**: The NiryatSetu system as a whole
- **User**: An MSME owner or employee using the platform
- **Buyer**: An international customer purchasing from an MSME through the platform

## User Stories

| ID | Role | Goal | Benefit |
|----|------|------|---------|
| US1 | MSME Owner | Classify my product with correct HS Code | Avoid customs delays and penalties |
| US2 | MSME Owner | Know import duties for target countries | Price my products competitively |
| US3 | MSME Owner | Discover optimal export pricing | Maximize profit while staying competitive |
| US4 | MSME Owner | Generate compliant export documents | Reduce documentation errors and rejections |
| US5 | MSME Owner | Communicate with international buyers in my language | Close deals without language barriers |
| US6 | MSME Owner | Track all my export deals in one place | Manage my export business efficiently |
| US7 | MSME Owner | Access the platform on my mobile phone | Work from anywhere without a computer |
| US8 | International Buyer | Find verified Indian suppliers | Source quality products with confidence |
| US9 | Platform Administrator | Monitor agent performance and accuracy | Ensure system reliability and compliance |
| US10 | Platform Administrator | Prevent hallucinated legal advice | Protect users from incorrect information |

## Commercial Viability

### Revenue Model

NiryatSetu operates on a hybrid SaaS revenue model combining subscription fees with transaction-based commissions:

**Subscription Tiers:**
- **Starter**: ₹999/month - 10 product classifications, 20 documents/month, basic support
- **Growth**: ₹2,999/month - 50 product classifications, 100 documents/month, priority support
- **Enterprise**: ₹9,999/month - Unlimited classifications, unlimited documents, dedicated account manager

**Transaction Commission:**
- 0.5% commission on successful export deals facilitated through the platform
- Minimum commission: ₹500 per transaction
- Maximum commission: ₹50,000 per transaction

### Market Opportunity

- **Target Market**: 63 million MSMEs in India
- **Addressable Market**: 5 million MSMEs with export potential (8% of total)
- **Initial Target**: 100,000 MSMEs in first 18 months (2% of addressable market)
- **Revenue Projection**: ₹300 crore ARR at 100,000 users (average ₹30,000/user/year)

### Cost Structure

- **AWS Infrastructure**: Estimated ₹5-8 lakh/month at scale (using Free Tier initially)
- **Customer Acquisition**: ₹2,000 per user (digital marketing, partnerships)
- **Support Operations**: ₹500 per user per year
- **Gross Margin**: 75-80% at scale

## Requirements

### Requirement 1: Product Classification and HS Code Determination

**User Story:** As an MSME owner, I want to classify my product with the correct HS Code by providing a photo or description, so that I can ensure customs compliance and avoid penalties.

#### Acceptance Criteria

1. WHEN a User uploads a product photo or provides a text description, THE Compliance_Agent SHALL analyze the input and determine the appropriate HS_Code within 10 seconds
2. WHEN the Compliance_Agent determines an HS_Code, THE Platform SHALL display the 6-digit international code and 8-digit country-specific code for the target market
3. WHEN multiple HS_Codes are possible, THE Compliance_Agent SHALL present up to 3 options ranked by confidence score with explanations
4. WHEN the User selects a target country (USA or UAE), THE Compliance_Agent SHALL retrieve the applicable import duty percentage for that HS_Code
5. IF the product description is ambiguous or insufficient, THEN THE Compliance_Agent SHALL request specific clarifying information from the User
6. THE Compliance_Agent SHALL use the Knowledge_Base containing DGFT Trade Policy data to validate classification accuracy
7. WHEN an HS_Code is determined, THE Platform SHALL store the classification result in DynamoDB linked to the User's product catalog
8. THE Compliance_Agent SHALL provide a confidence score (0-100%) for each HS_Code classification
9. WHEN the User requests explanation, THE Platform SHALL display the reasoning behind the HS_Code selection in simple language

### Requirement 2: Import Duty Calculation

**User Story:** As an MSME owner, I want to know the exact import duties my product will face in target countries, so that I can price my products competitively and transparently.

#### Acceptance Criteria

1. WHEN an HS_Code is determined, THE Compliance_Agent SHALL calculate the total import duty for USA and UAE markets
2. THE Platform SHALL display duty breakdown including: base tariff rate, additional duties, taxes, and total landed cost percentage
3. WHEN duty rates change in the Knowledge_Base, THE Platform SHALL flag affected products and notify the User within 24 hours
4. THE Compliance_Agent SHALL retrieve duty information from the Knowledge_Base containing Global Tariff Data
5. WHEN calculating duties, THE Platform SHALL consider any applicable Free Trade Agreements or preferential rates for Indian exports
6. THE Platform SHALL display duty calculations in both percentage and absolute Rupee amounts based on the User's product price
7. WHEN duty calculation is complete, THE Platform SHALL store the results in DynamoDB with timestamp and source references

### Requirement 3: Market Intelligence and Pricing Recommendations

**User Story:** As an MSME owner, I want to receive data-driven pricing recommendations based on global market analysis, so that I can maximize my profit while remaining competitive.

#### Acceptance Criteria

1. WHEN a User requests pricing guidance, THE Market_Intelligence_Agent SHALL analyze competitor pricing on Amazon.com and eBay for similar products
2. THE Market_Intelligence_Agent SHALL identify at least 10 comparable products and extract their pricing data
3. WHEN analyzing market data, THE Market_Intelligence_Agent SHALL calculate: minimum price, maximum price, median price, and recommended FOB_Price
4. THE Platform SHALL display the recommended FOB_Price in Indian Rupees with currency conversion using current exchange rates
5. THE Market_Intelligence_Agent SHALL consider: competitor prices, import duties, shipping costs, and target profit margin (minimum 20%) when recommending prices
6. WHEN market conditions change significantly (>10% price movement), THE Platform SHALL notify affected Users within 48 hours
7. THE Platform SHALL provide a pricing confidence score based on data quality and market volatility
8. WHEN displaying recommendations, THE Platform SHALL show price positioning (budget/mid-range/premium) relative to competitors
9. THE Platform SHALL store pricing analysis results in DynamoDB with timestamp and data sources

### Requirement 4: Export Documentation Generation

**User Story:** As an MSME owner, I want to automatically generate legally compliant export documents, so that I can reduce errors and speed up customs clearance.

#### Acceptance Criteria

1. WHEN a User initiates document generation, THE Documentation_Agent SHALL create a Proforma Invoice in PDF format within 15 seconds
2. THE Documentation_Agent SHALL generate a Packing List in PDF format containing all required fields per international standards
3. THE Documentation_Agent SHALL generate a Certificate of Origin in PDF format compliant with target country requirements
4. WHEN generating documents, THE Documentation_Agent SHALL populate all fields using data from: User profile, product catalog, HS_Code, and buyer information stored in DynamoDB
5. THE Platform SHALL validate that all mandatory fields are present before generating documents
6. IF required information is missing, THEN THE Platform SHALL prompt the User to provide the missing data with clear field labels
7. THE Platform SHALL apply the correct document template based on target country (USA or UAE) and product type
8. WHEN documents are generated, THE Platform SHALL store PDF files in Amazon S3 and record metadata in DynamoDB
9. THE Platform SHALL allow Users to download all three documents as a single ZIP file
10. THE Documentation_Agent SHALL ensure all currency amounts are displayed in both USD and INR
11. THE Platform SHALL include a unique document reference number on each generated document for tracking
12. THE Documentation_Agent SHALL use Guardrails to prevent generation of documents with hallucinated or incorrect legal information

### Requirement 5: Multilingual Negotiation Support

**User Story:** As an MSME owner, I want to communicate with international buyers in my local language, so that I can negotiate effectively without language barriers.

#### Acceptance Criteria

1. WHEN a Buyer sends a message in English or Spanish via WhatsApp, THE Negotiation_Bot SHALL translate it to the User's preferred language (Hindi or Tamil) within 5 seconds
2. WHEN a User responds in Hindi or Tamil, THE Negotiation_Bot SHALL translate the message to English before sending to the Buyer
3. THE Negotiation_Bot SHALL maintain conversation context across multiple message exchanges
4. THE Platform SHALL support the following language pairs: English↔Hindi, English↔Tamil, Spanish↔Hindi, Spanish↔Tamil
5. WHEN translation occurs, THE Negotiation_Bot SHALL preserve business terminology and product-specific terms accurately
6. THE Platform SHALL store all conversation history in DynamoDB with original and translated text
7. THE Negotiation_Bot SHALL detect the Buyer's language automatically from the first message
8. WHEN a User sets their preferred language in profile settings, THE Platform SHALL use that language for all incoming message translations
9. THE Negotiation_Bot SHALL handle common negotiation phrases (price inquiry, quantity discussion, delivery terms) with business-appropriate translations
10. IF translation confidence is low (<70%), THEN THE Platform SHALL flag the message for User review before sending

### Requirement 6: Multi-Agent Workflow Orchestration

**User Story:** As a platform user, I want the system to coordinate multiple AI agents seamlessly, so that I receive comprehensive export support without manual intervention between steps.

#### Acceptance Criteria

1. WHEN a User initiates an export workflow, THE Orchestrator SHALL coordinate execution of Compliance_Agent, Market_Intelligence_Agent, and Documentation_Agent in the correct sequence
2. THE Orchestrator SHALL pass output from the Compliance_Agent (HS_Code and duties) as input to the Market_Intelligence_Agent
3. THE Orchestrator SHALL pass combined output from both agents as input to the Documentation_Agent
4. WHEN any agent fails or times out (>30 seconds), THE Orchestrator SHALL retry the operation up to 3 times with exponential backoff
5. IF all retries fail, THEN THE Orchestrator SHALL log the error, notify the User, and allow manual intervention
6. THE Orchestrator SHALL maintain workflow state in DynamoDB to enable resume after failures
7. THE Platform SHALL display real-time progress updates to the User showing which agent is currently processing
8. WHEN the workflow completes successfully, THE Orchestrator SHALL store the complete result set in DynamoDB and notify the User
9. THE Orchestrator SHALL enforce timeout limits: 10 seconds for Compliance_Agent, 20 seconds for Market_Intelligence_Agent, 15 seconds for Documentation_Agent

### Requirement 7: Knowledge Base Management and RAG Implementation

**User Story:** As a platform administrator, I want the system to use authoritative trade data sources, so that users receive accurate and up-to-date compliance information.

#### Acceptance Criteria

1. THE Knowledge_Base SHALL contain the complete DGFT Trade Policy documentation in searchable format
2. THE Knowledge_Base SHALL contain Global Tariff Data for USA and UAE markets updated monthly
3. WHEN an agent queries the Knowledge_Base, THE Platform SHALL use RAG (Retrieval-Augmented Generation) to retrieve relevant context before generating responses
4. THE Platform SHALL use OpenSearch Serverless as the vector database backend for the Knowledge_Base
5. WHEN new trade policy documents are available, THE Platform SHALL support bulk upload and automatic indexing within 24 hours
6. THE Knowledge_Base SHALL support semantic search to find relevant information even when exact keywords don't match
7. THE Platform SHALL track Knowledge_Base query performance and maintain average retrieval time under 2 seconds
8. WHEN Knowledge_Base content is updated, THE Platform SHALL version the data and maintain audit logs of changes
9. THE Platform SHALL validate that retrieved context is relevant (relevance score >0.7) before using it in agent responses

### Requirement 8: User Profile and Product Catalog Management

**User Story:** As an MSME owner, I want to maintain my business profile and product catalog, so that the system can generate accurate documents and provide personalized recommendations.

#### Acceptance Criteria

1. WHEN a User registers, THE Platform SHALL collect: business name, GSTIN, IEC code, address, contact details, and preferred language
2. THE Platform SHALL validate GSTIN format (15 digits) and IEC code format (10 digits) before accepting registration
3. WHEN a User adds a product, THE Platform SHALL store: product name, description, photos, HS_Code, base price, and category in DynamoDB
4. THE Platform SHALL allow Users to upload up to 5 photos per product with maximum 5MB per photo
5. THE Platform SHALL support bulk product upload via CSV file with validation and error reporting
6. WHEN a User updates their profile, THE Platform SHALL validate all changes and update DynamoDB within 2 seconds
7. THE Platform SHALL implement Single Table Design in DynamoDB with partition key: USER#{userId} and sort key patterns for different entity types
8. THE Platform SHALL allow Users to categorize products using standard export categories (Textiles, Handicrafts, Food Products, etc.)
9. WHEN a User deletes a product, THE Platform SHALL soft-delete the record and maintain history for 90 days

### Requirement 9: Deal Flow Management

**User Story:** As an MSME owner, I want to track all my export inquiries and deals in one place, so that I can manage my export business efficiently.

#### Acceptance Criteria

1. WHEN a Buyer inquiry is received, THE Platform SHALL create a Deal_Flow record in DynamoDB with status: "New Inquiry"
2. THE Platform SHALL track Deal_Flow through stages: New Inquiry → Quotation Sent → Negotiation → Documents Generated → Deal Closed
3. WHEN a User updates Deal_Flow status, THE Platform SHALL record the timestamp and status change in DynamoDB
4. THE Platform SHALL display a dashboard showing all active deals with current status and next action required
5. WHEN a deal reaches "Documents Generated" status, THE Platform SHALL automatically link the generated documents to the Deal_Flow record
6. THE Platform SHALL calculate and display deal value in both USD and INR for each Deal_Flow
7. WHEN a deal is marked "Deal Closed", THE Platform SHALL calculate the commission amount and update billing records
8. THE Platform SHALL allow Users to filter and search deals by: status, buyer country, product, date range, and value
9. THE Platform SHALL send reminder notifications when a deal has been inactive for more than 7 days

### Requirement 10: Security and Guardrails

**User Story:** As a platform administrator, I want to prevent the AI agents from providing hallucinated or incorrect legal advice, so that users can trust the platform's recommendations.

#### Acceptance Criteria

1. THE Platform SHALL implement Amazon Bedrock Guardrails on all agent responses before displaying to Users
2. THE Guardrails SHALL block responses containing: made-up legal requirements, fabricated duty rates, or invented compliance rules
3. WHEN Guardrails detect potentially harmful content, THE Platform SHALL block the response and log the incident for review
4. THE Platform SHALL validate all HS_Code classifications against the Knowledge_Base before presenting to Users
5. THE Platform SHALL validate all duty calculations against authoritative tariff data before displaying
6. WHEN an agent response cannot be validated, THE Platform SHALL display a warning: "This information requires manual verification"
7. THE Guardrails SHALL prevent disclosure of other users' business information or trade secrets
8. THE Platform SHALL implement content filtering to block: profanity, discriminatory language, and inappropriate content in all user inputs and agent outputs
9. THE Platform SHALL log all Guardrails interventions with timestamp, agent, and reason for audit purposes
10. THE Platform SHALL maintain a confidence threshold of 80% for all agent responses; responses below this threshold SHALL be flagged for human review

### Requirement 11: Mobile-First User Interface

**User Story:** As an MSME owner, I want to access the platform on my mobile phone, so that I can manage my export business from anywhere without needing a computer.

#### Acceptance Criteria

1. THE Platform SHALL provide a responsive React web application that adapts to mobile screen sizes (320px to 428px width)
2. WHEN accessed on mobile, THE Platform SHALL display a simplified navigation menu optimized for touch interaction
3. THE Platform SHALL support touch gestures: swipe for navigation, pinch-to-zoom for documents, pull-to-refresh for data updates
4. THE Platform SHALL load the initial page within 3 seconds on 4G mobile networks
5. THE Platform SHALL optimize image loading using lazy loading and responsive image sizes
6. WHEN a User uploads photos on mobile, THE Platform SHALL support direct camera capture in addition to gallery selection
7. THE Platform SHALL display all forms with mobile-optimized input fields and keyboards (numeric for prices, email for email fields)
8. THE Platform SHALL work offline for viewing previously loaded data and sync changes when connection is restored
9. THE Platform SHALL use progressive web app (PWA) features to enable "Add to Home Screen" functionality
10. THE Platform SHALL display all text in readable font sizes (minimum 14px) without requiring zoom on mobile devices

### Requirement 12: Performance and Scalability

**User Story:** As a platform user, I want the system to respond quickly even during peak usage, so that I can complete time-sensitive export tasks without delays.

#### Acceptance Criteria

1. THE Platform SHALL respond to user interactions within 2 seconds for 95% of requests under normal load
2. WHEN processing agent requests, THE Platform SHALL complete end-to-end workflows within 60 seconds for 99% of cases
3. THE Platform SHALL support at least 1,000 concurrent users without performance degradation
4. THE Platform SHALL scale automatically using AWS services to handle traffic spikes up to 5x normal load
5. THE Platform SHALL implement caching for frequently accessed data (HS_Code lookups, duty rates) with 1-hour TTL
6. WHEN DynamoDB operations occur, THE Platform SHALL use batch operations where possible to minimize latency
7. THE Platform SHALL implement API rate limiting: 100 requests per minute per user for standard tier, 500 for enterprise tier
8. THE Platform SHALL monitor and log performance metrics: response time, error rate, agent processing time, and database latency
9. WHEN system load exceeds 80% capacity, THE Platform SHALL trigger auto-scaling and send alerts to administrators

### Requirement 13: Authentication and Authorization

**User Story:** As an MSME owner, I want secure access to my account and data, so that my business information remains confidential.

#### Acceptance Criteria

1. THE Platform SHALL support user registration with email verification before account activation
2. THE Platform SHALL implement password requirements: minimum 8 characters, at least one uppercase, one lowercase, one number, one special character
3. WHEN a User logs in, THE Platform SHALL issue a JWT token valid for 24 hours
4. THE Platform SHALL support "Remember Me" functionality extending session to 30 days with secure token storage
5. THE Platform SHALL implement role-based access control with roles: Owner, Manager, Viewer
6. WHEN a User with "Viewer" role attempts restricted actions, THE Platform SHALL deny access and display appropriate error message
7. THE Platform SHALL support password reset via email with time-limited (1 hour) reset tokens
8. THE Platform SHALL implement account lockout after 5 failed login attempts for 30 minutes
9. THE Platform SHALL log all authentication events (login, logout, failed attempts) in DynamoDB for security audit
10. THE Platform SHALL support multi-factor authentication (MFA) via SMS OTP for enterprise tier users

### Requirement 14: Integration and API Design

**User Story:** As a platform administrator, I want well-designed APIs, so that the system components integrate seamlessly and future integrations are possible.

#### Acceptance Criteria

1. THE Platform SHALL expose RESTful APIs through Amazon API Gateway with OpenAPI 3.0 specification
2. THE Platform SHALL implement API endpoints for: product management, classification requests, pricing analysis, document generation, and deal management
3. WHEN an API request is received, THE Platform SHALL validate the request payload against JSON schemas before processing
4. THE Platform SHALL return standardized error responses with HTTP status codes and descriptive error messages
5. THE Platform SHALL implement API versioning using URL path (e.g., /v1/products) to support backward compatibility
6. THE Platform SHALL support pagination for list endpoints with default page size of 20 and maximum of 100
7. THE Platform SHALL include CORS headers to allow frontend access from authorized domains
8. WHEN API errors occur, THE Platform SHALL return error responses in format: {error: {code, message, details}}
9. THE Platform SHALL implement request/response logging for all API calls with correlation IDs for tracing
10. THE Platform SHALL document all APIs with examples, request/response schemas, and authentication requirements

### Requirement 15: Monitoring and Observability

**User Story:** As a platform administrator, I want comprehensive monitoring and logging, so that I can identify and resolve issues quickly.

#### Acceptance Criteria

1. THE Platform SHALL log all agent invocations with: timestamp, agent type, input, output, processing time, and success/failure status
2. THE Platform SHALL implement structured logging in JSON format for all application logs
3. WHEN errors occur, THE Platform SHALL log stack traces and context information for debugging
4. THE Platform SHALL track business metrics: daily active users, classifications performed, documents generated, deals closed, revenue generated
5. THE Platform SHALL track technical metrics: API response times, agent success rates, Knowledge_Base query performance, DynamoDB latency
6. THE Platform SHALL send alerts when: error rate exceeds 5%, agent success rate drops below 90%, API response time exceeds 5 seconds
7. THE Platform SHALL maintain logs for 90 days in CloudWatch Logs with automatic archival to S3 for long-term storage
8. THE Platform SHALL provide a dashboard displaying: system health, active users, agent performance, and revenue metrics
9. WHEN critical errors occur (agent failures, database unavailability), THE Platform SHALL send immediate notifications via email and SMS
10. THE Platform SHALL implement distributed tracing to track requests across multiple AWS services

## Non-Functional Requirements

### Performance

1. THE Platform SHALL maintain 99.5% uptime measured monthly
2. THE Platform SHALL support peak load of 5,000 concurrent users
3. THE Platform SHALL process 10,000 agent requests per hour during peak times
4. THE Platform SHALL maintain average API response time under 500ms
5. THE Platform SHALL complete document generation within 15 seconds for 99% of requests

### Security

1. THE Platform SHALL encrypt all data in transit using TLS 1.3
2. THE Platform SHALL encrypt all data at rest using AWS KMS
3. THE Platform SHALL implement least-privilege IAM policies for all AWS services
4. THE Platform SHALL conduct security audits quarterly
5. THE Platform SHALL comply with GDPR for handling user data
6. THE Platform SHALL implement data backup with 24-hour recovery point objective (RPO)
7. THE Platform SHALL sanitize all user inputs to prevent injection attacks

### Scalability

1. THE Platform SHALL scale horizontally to handle 10x traffic growth without architecture changes
2. THE Platform SHALL use serverless services (Lambda, DynamoDB, Bedrock) to enable automatic scaling
3. THE Platform SHALL implement database sharding strategy for growth beyond 1 million users
4. THE Platform SHALL design APIs to support future mobile native applications

### Usability

1. THE Platform SHALL support English, Hindi, and Tamil user interfaces
2. THE Platform SHALL provide contextual help and tooltips for all complex features
3. THE Platform SHALL display loading indicators for all operations taking more than 1 second
4. THE Platform SHALL provide clear error messages with suggested remediation steps
5. THE Platform SHALL follow WCAG 2.1 Level AA accessibility guidelines

### Maintainability

1. THE Platform SHALL use infrastructure-as-code (AWS CDK or CloudFormation) for all AWS resources
2. THE Platform SHALL implement automated testing with minimum 80% code coverage
3. THE Platform SHALL use semantic versioning for all releases
4. THE Platform SHALL maintain API documentation automatically generated from code
5. THE Platform SHALL implement feature flags for gradual rollout of new capabilities

### Cost Optimization

1. THE Platform SHALL use Amazon Nova Micro model to stay within AWS Free Tier during initial phase
2. THE Platform SHALL implement DynamoDB on-demand pricing for cost efficiency at low volumes
3. THE Platform SHALL use S3 Intelligent-Tiering for document storage cost optimization
4. THE Platform SHALL monitor and alert when monthly AWS costs exceed budget thresholds
5. THE Platform SHALL implement automatic cleanup of unused resources (old documents, expired sessions)

### Compliance

1. THE Platform SHALL comply with Indian data localization requirements (data stored in ap-south-1 region)
2. THE Platform SHALL maintain audit logs for all financial transactions for 7 years
3. THE Platform SHALL implement data retention policies: user data for 3 years after account closure
4. THE Platform SHALL provide data export functionality for users to download their complete data
5. THE Platform SHALL implement "Right to be Forgotten" functionality for GDPR compliance
