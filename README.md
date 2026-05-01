# Food Ordering System

An advanced software development project demonstrating both monolithic and microservices architectures for a complete food ordering platform. This project uses the C4 Model to visualize system architecture across four levels: System Context, Containers, Components, and Code.

**Course**: Advanced Software Development - Assignment #3  
**Instructor**: Mustafa Assaf  
**Author**: Hamza Nasser (12324183)

## Overview

The Food Ordering System is a comprehensive food ordering platform that enables customers to browse restaurants, place orders, track deliveries, and manage loyalty rewards. The system includes payment processing, real-time notifications, order dispute resolution, and analytics capabilities.

The project implements **two architectural approaches**:
- **Monolithic Architecture**: Single backend handling all business logic
- **Microservices Architecture**: Independent services handling specific business domains

## Key Features

### Core Functionality
- **User Authentication**: Secure login/registration via AWS Cognito
- **Order Management**: Complete order lifecycle from placement to delivery
- **Menu Browsing**: Search and view restaurants and menu items
- **Payment Processing**: Secure payments through Stripe integration
- **Order Tracking**: Real-time delivery tracking for customers
- **Dispute Resolution**: Admins handle order disputes and customer issues
- **Loyalty & Rewards**: Track reward points and loyalty-based discounts
- **Analytics**: System-wide metrics and business intelligence
- **Notifications**: Order confirmations and status updates via push/email

### External Integrations
- **AWS Cognito**: User authentication and session management
- **Stripe**: Payment gateway for credit card processing and refunds
- **Push/Email Provider**: FCM, APNs, or SES for customer notifications
- **Loyalty Provider**: Tracks and manages customer reward programs

## Architecture Comparison

### Monolithic Architecture

**Containers:**
- **Web App**: React-based browser interface
- **Mobile App**: iOS/Android native applications
- **Monolithic Backend**: Single Node.js/Java application
- **Database**: Single PostgreSQL database

**Characteristics:**
- Single deployment unit
- All modules in shared codebase
- Unified database with potential coupling
- Faster initial development
- Harder to scale individual components

### Microservices Architecture

**Services:**
- **Order Service**: Manages order lifecycle and state transitions
- **Inventory Service**: Tracks stock levels and availability
- **Payment Service**: Handles payment processing with Stripe
- **User Service**: Authentication and user management (Cognito)
- **Notification Service**: Sends order confirmations and updates
- **Tracking Service**: Real-time order tracking and logistics
- **Dispute Service**: Handles customer disputes and resolutions
- **Discount Service**: Manages promotional codes and loyalty discounts (NEW)
- **Analytics Service**: Collects metrics and generates reports (NEW)

**Infrastructure:**
- **API Gateway**: Single entry point (Kong/NGINX/AWS API Gateway)
- **Databases**: Separate PostgreSQL database per service
- **Message Queue**: Kafka for async event communication

**Characteristics:**
- Independent deployment per service
- Technology flexibility per service
- Isolated scaling
- Better fault isolation
- Higher operational complexity

## C4 Model Architecture

### Level 1: System Context
Shows how users and admins interact with the system, and how the system integrates with external services (Cognito, Stripe, Notification Provider, Loyalty Provider).

### Level 2: Containers
Identifies all major containers and their communication patterns:
- Client applications (Web/Mobile)
- API Gateway (for microservices)
- Individual services or monolithic backend
- Data storage systems

### Level 3: Components
Details internal structure of each service:
- **Controllers**: Handle HTTP requests and validation
- **Workflows**: Implement business logic and orchestration
- **Repositories**: Abstract database access

Example - Order Service Components:
```
OrderController → validates requests, routes to workflow
OrderWorkflow → implements order logic, calls other services
OrderRepository → manages database operations
```

### Level 4: Code
Implementation details at the code level (classes, functions, databases).

## Scaling Strategy

### Monolithic Approach
- **Only Horizontal Scaling**: Add more instances of entire application
- **Problem**: Scales everything even if only one module needs resources
- **Cost**: Inefficient resource usage during peak and off-peak periods

### Microservices Approach

**Selective Scaling Per Service:**
- **Order Service**: Horizontal (stateless, high request volume during meal times)
- **Payment Service**: Horizontal (stateless transaction processing)
- **Inventory Service**: Horizontal (concurrent stock checks)
- **Notification Service**: Horizontal (high volume of notifications)
- **Analytics Service**: Vertical (CPU/memory intensive data processing)
- **Discount Service**: Horizontal (read-heavy validation requests)
- **API Gateway**: Hybrid (horizontal instances + vertical power)

## Key Responsibilities

### Monolithic Backend
- HTTP request handling
- All business logic orchestration
- External service integrations (Stripe, Cognito, notifications, loyalty)
- Database operations
- Error handling for all features

### Microservices - Service Responsibilities

| Service | Responsibility |
|---------|-----------------|
| **Order Service** | Order creation, state management, lifecycle |
| **Payment Service** | Payment processing with Stripe, transaction handling |
| **User Service** | Authentication via Cognito, user profile management |
| **Inventory Service** | Stock levels, availability checks |
| **Notification Service** | Order confirmations, status updates |
| **Tracking Service** | Delivery tracking, driver assignment |
| **Dispute Service** | Complaint handling, refund processing |
| **Discount Service** | Promo codes, coupon validation, loyalty discounts |
| **Analytics Service** | Event collection, metrics aggregation, reporting |

## Benefits of Microservices vs Monolith

### Fault Isolation
- **Monolith**: Stripe API down → entire backend affected
- **Microservices**: Stripe down → only Payment Service affected, others continue

### Independent Updates
- **Monolith**: Update Stripe integration → full regression testing required
- **Microservices**: Payment Service team updates independently

### Resource Optimization
- **Monolith**: One Payment Module slow → must scale entire application
- **Microservices**: Scale only the Payment Service

### Team Organization
- **Monolith**: All teams share codebase
- **Microservices**: Each team owns complete service end-to-end

## Order Processing Flow (Microservices)

1. **Client** → API Gateway (HTTP Request)
2. **Order Service** receives request, validates order
3. **Order Service** → Inventory Service (Check stock)
4. **Order Service** → Discount Service (Validate coupon)
5. **Order Service** → Payment Service (Process payment)
6. **Payment Service** → Stripe (Process transaction)
7. **Order Service** → Tracking Service (Create tracking)
8. **Order Service** → Notification Service (Send confirmation)
9. All Services → Analytics Service (Log events)

## Key Learnings & Considerations

### Transaction Management
- Handling partial failures across services (e.g., payment succeeds but inventory fails)
- Distributed transaction coordination needed
- Consider saga pattern or event sourcing

### Performance Challenges
- Synchronous service calls can create bottlenecks
- Order creation requires multiple dependent calls
- Consider async messaging for non-critical updates
- Analytics service can become a bottleneck if not carefully designed

### Tight Coupling Risks
- OrderWorkflow depends on multiple services
- API contract changes require coordinated updates
- Need clear communication protocols between teams

### Operational Complexity
- More services = more monitoring and debugging
- Microservices harder to test end-to-end
- Need comprehensive logging and tracing
- Higher infrastructure requirements

## References

- **C4 Model**: https://c4model.com/
- **Microservices Patterns**: Chris Richardson's microservices.io
- **AWS Cognito**: https://aws.amazon.com/cognito/
- **Stripe API**: https://stripe.com/docs
- **Kong API Gateway**: https://konghq.com/

## Author

**Hamza Nasser** 

## Acknowledgments

- Instructor: Mustafa Assaf
- C4 Model for architecture visualization
- Industry best practices for food ordering systems
