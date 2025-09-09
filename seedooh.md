# Seedooh API Integration

This document provides comprehensive documentation for the Seedooh API integration, including enterprise-grade API endpoints, advanced security features, comprehensive error handling, and testing procedures.

## 🚀 API Endpoints

### 1. Campaigns Fetch API

- **Endpoint**: `POST /api/seedooh/campaigns_fetch.php`
- **Purpose**: Fetch Seedooh-verified campaigns with advanced filtering and memory optimization
- **Authentication**: Bearer token or session-based
- **Request Body**:
```json
{
  "campaign_id": 123,
    "start_date_time": "2024-01-01T00:00:00",
    "end_date_time": "2024-01-31T23:59:59",
    "min_bookings": 0,
    "max_bookings": 100,
    "campaign_statuses": ["active", "pending"],
    "campaign_tag_ids": [1, 2, 3],
    "mediaplayer_ids": [1, 2, 3],
    "mediaplayer_tag_ids": [1, 2, 3],
    "mediaplayer_cat_ids": [1, 2, 3],
    "sort_field": "campaign_id",
    "sort_order": "asc",
    "by_group": 1
  }
  ```
- **Response**: Array of campaign objects with Seedooh verification status and creative schedules
- **Features**: 
  - ✅ Input validation and sanitization
  - ✅ Memory-efficient processing for large datasets
  - ✅ Security headers and CSRF protection
  - ✅ Comprehensive error handling and logging

### 2. Players Fetch API

- **Endpoint**: `POST /api/seedooh/players_fetch.php`
- **Purpose**: Search and fetch player information with advanced filtering
- **Authentication**: Bearer token or session-based
- **Request Body**:
```json
{
    "company_group_id": 48,
    "timezone": "Australia/Sydney",
    "search": "optional search term",
    "player_ids": [1, 2, 3],
    "tags": ["tag1", "tag2"],
    "categories": ["cat1", "cat2"],
    "states": ["VIC", "NSW"],
    "formats": ["LED", "LCD"],
    "resolutions": ["1920x1080", "384x960"],
    "limit": 100,
    "offset": 0
  }
  ```
- **Response**: Array of player objects with location, technical details, and screenshot information
- **Features**:
  - ✅ Advanced search and filtering capabilities
  - ✅ Pagination support
  - ✅ Input validation and sanitization
  - ✅ Security headers and CSRF protection

### 3. Campaign Playlogs Generator API

- **Endpoint**: `POST /api/seedooh/campaign_playlogs_generator.php`
- **Purpose**: Initiate campaign playlogs report generation with comprehensive validation
- **Authentication**: Bearer token or session-based
- **Request Body**:
```json
{
    "record_output_type": {
      "Seedooh": 123
    },
    "datetime_range": {
      "start": "2024-01-01T00:00:00",
      "end": "2024-01-31T23:59:59"
    },
    "player_ids": [1, 2, 3]
  }
  ```
- **Response**: Report ID for tracking generation status
- **Features**:
  - ✅ Comprehensive input validation
  - ✅ Campaign access verification
  - ✅ NATS integration for report generation
  - ✅ Detailed error logging and handling

### 4. Campaign Playlogs Fetch API

- **Endpoint**: `POST /api/seedooh/campaign_playlogs_fetch.php`
- **Purpose**: Fetch generated playlogs report status and data with Azure Blob integration
- **Authentication**: Bearer token or session-based
- **Request Body**:
```json
{
    "id": "report_123"
  }
  ```
- **Response**: Report status, SQL file URL, and parsed playlogs data
- **Features**:
  - ✅ Azure Blob Storage integration
  - ✅ SQL file parsing and validation
  - ✅ Memory-efficient data processing
  - ✅ Comprehensive error handling

## 🛡️ Enterprise Security Features

### Input Validation & Sanitization
- **Comprehensive Validation**: All inputs are validated using `SeedoohRequestValidator` class
- **SQL Injection Prevention**: All database queries use prepared statements
- **XSS Protection**: Input sanitization prevents cross-site scripting attacks
- **Data Type Validation**: Strict type checking for all parameters

### Security Headers
- **Content Security Policy (CSP)**: Prevents code injection attacks
- **HTTP Strict Transport Security (HSTS)**: Enforces HTTPS connections
- **X-Frame-Options**: Prevents clickjacking attacks
- **X-Content-Type-Options**: Prevents MIME type sniffing
- **CSRF Protection**: Token-based protection for state-changing operations

### Error Handling & Logging
- **Centralized Error Management**: `SeedoohErrorHandler` class for consistent error handling
- **Structured Logging**: Detailed error logs with context and request tracking
- **Security-First Error Messages**: No sensitive information leaked in error responses
- **Request ID Tracking**: Unique request IDs for debugging and monitoring

### Memory Management
- **Chunked Processing**: `SeedoohMemoryManager` handles large datasets efficiently
- **Memory Monitoring**: Automatic memory usage tracking and warnings
- **Garbage Collection**: Optimized memory cleanup for long-running processes
- **Stack Overflow Prevention**: Memory limits prevent system crashes

## 📋 Response Formats

### Success Response Format
All APIs return consistent JSON responses:

```json
{
  "success": true,
  "data": {
    // API-specific data
  },
  "meta": {
    "request_id": "req_1234567890",
    "timestamp": "2024-01-01T12:00:00Z",
    "execution_time": "0.123s"
  }
}
```

### Error Response Format
Consistent error responses across all APIs:

```json
{
  "success": false,
  "error": "Error message",
  "code": 400,
  "details": {
  "validation_errors": {
      "field_name": "Error description"
    }
  },
  "request_id": "req_1234567890",
  "timestamp": "2024-01-01T12:00:00Z"
}
```

### HTTP Status Codes
- **200**: Success
- **400**: Bad Request (validation errors)
- **401**: Unauthorized (authentication required)
- **404**: Not Found (resource not found)
- **405**: Method Not Allowed (wrong HTTP method)
- **500**: Internal Server Error (server error)
- **503**: Service Unavailable (NATS/Azure unavailable)

## 🏗️ Helper Classes Architecture

### Core Helper Classes

#### 1. SeedoohRequestValidator
- **Purpose**: Comprehensive input validation and sanitization
- **Features**:
  - Type validation for all input parameters
  - Data sanitization to prevent XSS attacks
  - Custom validation rules for each API endpoint
  - Detailed error reporting with field-specific messages

#### 2. SeedoohErrorHandler
- **Purpose**: Centralized error handling and logging
- **Features**:
  - Structured error logging with context
  - Security-first error messages
  - Request ID tracking for debugging
  - Different error types (validation, API, database, NATS)

#### 3. SeedoohMemoryManager
- **Purpose**: Memory-efficient processing for large datasets
- **Features**:
  - Chunked processing for large result sets
  - Memory usage monitoring and warnings
  - Automatic garbage collection
  - Stack overflow prevention

#### 4. SeedoohSecurityHeaders
- **Purpose**: Security headers and CSRF protection
- **Features**:
  - Content Security Policy (CSP) headers
  - HTTP Strict Transport Security (HSTS)
  - X-Frame-Options and X-Content-Type-Options
  - CSRF token generation and validation
  - Rate limiting capabilities

#### 5. SeedoohSerdeDeserializer
- **Purpose**: Deserialize JSON messages from Rust services
- **Features**:
  - Automatic JSON deserialization
  - NATS protocol support
  - Type-safe deserialization
  - Error handling and validation

## 🔗 Integration with Existing APIs

The helper classes are integrated with all Seedooh APIs:

1. **campaigns_fetch.php**: Uses validator, error handler, memory manager, and security headers
2. **players_fetch.php**: Uses validator and security headers
3. **campaign_playlogs_generator.php**: Uses validator, error handler, and memory manager
4. **campaign_playlogs_fetch.php**: Uses validator, error handler, memory manager, and deserializer
5. **NATS Communicator**: Handles all NATS message deserialization

## 📊 Performance & Monitoring

### Performance Features
- **Memory Optimization**: Chunked processing for large datasets
- **Response Time Tracking**: Built-in execution time monitoring
- **Memory Usage Monitoring**: Automatic memory usage tracking and warnings
- **Database Query Optimization**: Prepared statements and efficient queries

### Monitoring & Logging
- **Structured Logging**: JSON-formatted logs with context
- **Request Tracking**: Unique request IDs for debugging
- **Error Categorization**: Different error types for better monitoring
- **Performance Metrics**: Response time and memory usage tracking

### Log Files
- **Error Logs**: `/log/seedooh/seedooh_errors_YYYY-MM-DD.log`
- **Debug Logs**: `/log/seedooh/seedooh_debug_YYYY-MM-DD.log`
- **Performance Logs**: `/log/seedooh/seedooh_performance_YYYY-MM-DD.log`

## 🔧 Troubleshooting

### Common Issues

1. **Empty Response**: Check NATS connection and subject names
2. **JSON Decode Errors**: Verify message format from Rust service
3. **Missing Fields**: Check required field validation
4. **Timeout Issues**: Increase timeout values for slow responses
5. **Memory Issues**: Check memory limits and chunked processing
6. **Authentication Errors**: Verify API keys and session state

### Debug Steps

1. **Enable Debug Logging**: `define('SEEDOOH_DEBUG', true);`
2. **Check Error Logs**: Review structured error logs for details
3. **Validate Message Format**: Ensure Rust service sends correct format
4. **Test with Sample Data**: Use provided test suite
5. **Monitor Memory Usage**: Check memory manager logs
6. **Verify Security Headers**: Ensure all security features are enabled

### Performance Optimization

1. **Memory Management**: Use chunked processing for large datasets
2. **Database Optimization**: Ensure prepared statements are used
3. **Caching**: Implement caching for frequently accessed data
4. **Connection Pooling**: Optimize database connections
5. **Error Handling**: Minimize error handling overhead

## 🚀 Production Deployment

### Prerequisites
- PHP 8.0+ with required extensions
- MySQL 8.0+ with prepared statement support
- NATS server for message queuing
- Azure Blob Storage for file storage
- Proper SSL/TLS configuration

### Security Checklist
- ✅ Input validation enabled
- ✅ SQL injection prevention active
- ✅ Security headers configured
- ✅ CSRF protection enabled
- ✅ Error logging configured
- ✅ Memory limits set appropriately
- ✅ Rate limiting configured
- ✅ Authentication properly implemented

### Monitoring Setup
- Configure log rotation for error logs
- Set up monitoring for memory usage
- Monitor response times and error rates
- Set up alerts for critical errors
- Configure backup for log files

[Home](README.md)
