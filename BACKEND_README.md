# 🚀 IICT Hackathon Registration System - Backend

A production-ready, scalable backend system for hackathon team registration and management, built with Next.js 15, MongoDB, and Redis.

## 🏗️ System Architecture

### **Technology Stack**
- **Framework**: Next.js 15.3.5 with App Router & Server Actions
- **Database**: MongoDB with Mongoose ODM
- **Cache & Rate Limiting**: Redis with IORedis client
- **File Storage**: Cloudinary for document uploads
- **State Management**: Zustand for client-side state
- **Validation**: Custom validation with security-first approach

### **Key Features**
- ✅ Multi-step team registration system (1-4 participants)
- ✅ Production-grade rate limiting with Redis
- ✅ File upload handling for idea documents (PDF)
- ✅ Real-time form validation and step management
- ✅ College/Company autocomplete with dynamic creation
- ✅ Social profile validation (GitHub, LinkedIn, DevFolio)
- ✅ Click tracking and analytics
- ✅ Comprehensive error handling and logging
- ✅ Security-first design with input sanitization

## 🔧 API Architecture

### **Core Endpoints**

#### 1. Team Registration API
**`POST /api/teamRegistration`**
- Multi-part form handling with file uploads
- Comprehensive validation for all participant data
- Cloudinary integration for document storage
- Team name uniqueness validation
- Rate limited: 20 registrations/hour per IP

```typescript
interface TeamRegistration {
  team_name: string;
  team_size: number (1-4);
  participants: Participant[];
  idea_title: string;
  idea_document_url: string;
  status: 'registered' | 'approved' | 'rejected';
}
```

#### 2. College Management API
**`GET /api/colleges?search=query`**
- Fuzzy search with regex sanitization
- Dynamic college creation support
- Rate limited: 500 requests/15 minutes

**`POST /api/colleges`** (Internal)
- Secure college creation with validation
- Prevents duplicate entries

#### 3. Form Validation API
**`POST /api/validateStep`**
- Real-time step-by-step validation
- Email uniqueness checking
- Phone number format validation
- Social profile URL validation
- Custom college creation handling

#### 4. Analytics API
**`POST /api/clickTracking`**
- Button click tracking for UX analytics
- IP-based user tracking
- User agent and referrer logging

## 🔒 Security Features

### **Rate Limiting System**
Production-only implementation with Redis backend:

| Endpoint | Limit | Duration | Block Duration |
|----------|--------|-----------|----------------|
| General APIs | 500 requests | 15 min | 5 min |
| Team Registration | 20 submissions | 1 hour | 15 min |
| File Uploads | 50 uploads | 1 hour | 30 min |
| College Creation | 50 entries | 1 hour | 30 min |

### **Input Validation & Sanitization**
- **Regex Input Sanitization**: Prevents ReDoS attacks
- **Email Validation**: RFC-compliant email format checking
- **Phone Validation**: Indian mobile number format (10 digits, starts with 6-9)
- **Age Validation**: Range validation (1-120 years)
- **File Validation**: PDF format, size limits, secure upload handling

### **Data Security**
- **MongoDB Injection Prevention**: Parameterized queries with Mongoose
- **CORS Configuration**: Properly configured for production
- **Environment Variable Protection**: Secure credential management
- **Input Trimming & Case Normalization**: Consistent data formatting

## 📊 Database Schema

### **Team Registration Collection**
```javascript
{
  team_name: { type: String, required: true, unique: true },
  team_size: { type: Number, min: 1, max: 4 },
  selected: { type: Boolean, default: false },
  idea_title: { type: String, required: true },
  idea_document_url: { type: String, required: true },
  participants: [{
    name: { type: String, required: true },
    email: { type: String, required: true, lowercase: true },
    age: { type: Number, min: 1, max: 120 },
    phone: { type: String, required: true },
    student_or_professional: { enum: ['student', 'professional'] },
    college_or_company_name: { type: String, required: true },
    github_profile: { type: String },
    linkedin_profile: { type: String },
    devfolio_profile: { type: String }
  }],
  status: { 
    enum: ['registered', 'approved', 'rejected'],
    default: 'registered'
  },
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now }
}
```

### **College Collection**
```javascript
{
  name: { type: String, required: true, trim: true },
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now }
}
```

### **Click Tracking Collection**
```javascript
{
  buttonType: { 
    enum: ['hero_register', 'navbar_register_desktop', 'navbar_register_mobile']
  },
  userAgent: { type: String },
  ipAddress: { type: String },
  referrer: { type: String },
  timestamp: { type: Date, default: Date.now }
}
```

## ☁️ Third-Party Integrations

### **Cloudinary Configuration**
- **Document Storage**: Secure PDF upload and storage
- **Folder Organization**: `iict-hackathon/idea-documents/`
- **File Validation**: Type checking, size limits
- **Unique Naming**: Prevents file conflicts

### **Redis Integration**
- **Connection Management**: Lazy connection with retry logic
- **Rate Limiting**: Distributed rate limiting across instances
- **Error Handling**: Graceful degradation on Redis failures

## 🚀 Performance Optimizations

### **Database Optimizations**
- **Connection Pooling**: Mongoose connection management
- **Query Optimization**: Selective field querying with `.lean()`
- **Indexing Strategy**: Unique indexes on team names, email searching
- **Connection Guards**: Prevents multiple concurrent connections

### **Caching Strategy**
- **Redis Caching**: Rate limit data caching
- **Client-Side State**: Zustand for form state persistence
- **Static Asset Optimization**: Next.js automatic optimizations

### **Error Handling**
- **Graceful Degradation**: Proper error responses for all scenarios
- **Logging Strategy**: Comprehensive error logging for debugging
- **User-Friendly Messages**: Clear error messages for frontend

## 🔧 Development Setup

### **Environment Variables**
```env
# Database
MONGODB_URI=mongodb://localhost:27017/iict-hack

# Redis (Required for production)
REDIS_URL=redis://localhost:6379

# Cloudinary
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
```

### **Installation & Setup**
```bash
# Install dependencies
npm install

# Development server with Turbopack
npm run dev

# Production build
npm run build
npm start
```

## 📈 Monitoring & Analytics

### **Built-in Analytics**
- **Registration Funnel Tracking**: Step completion rates
- **Click-through Analytics**: Button interaction tracking
- **Error Rate Monitoring**: Failed validation tracking
- **Performance Metrics**: API response times

### **Production Monitoring**
- **Rate Limiting Logs**: Redis-based monitoring
- **Database Connection Health**: MongoDB connection status
- **File Upload Success Rates**: Cloudinary integration monitoring

## 🏆 Production-Ready Features

### **Scalability**
- **Horizontal Scaling**: Stateless design supports multiple instances
- **Database Scaling**: MongoDB clustering support
- **CDN Integration**: Cloudinary for global file distribution
- **Caching Layer**: Redis for distributed caching

### **Reliability**
- **Error Recovery**: Comprehensive error handling
- **Data Validation**: Multi-layer validation (client + server)
- **Transaction Safety**: Atomic operations for data consistency
- **Backup Strategy**: MongoDB backup-friendly schema design

### **Security Compliance**
- **Data Privacy**: GDPR-compliant data handling
- **Input Validation**: Prevents common security vulnerabilities
- **Rate Limiting**: DDoS protection
- **Secure File Handling**: Safe file upload and processing

---

## 🎯 Technical Achievements

This backend demonstrates proficiency in:
- **Full-Stack Development**: Next.js API routes with TypeScript
- **Database Design**: MongoDB schema design and optimization
- **Security Implementation**: Production-grade security measures
- **Third-Party Integration**: Cloudinary and Redis integration
- **Performance Optimization**: Caching and query optimization
- **Error Handling**: Comprehensive error management
- **DevOps Ready**: Environment-based configuration management

**Live Demo**: [https://iict-hack.vercel.app/](https://iict-hack.vercel.app/)

**Repository**: Private (Available upon request for portfolio review)

---

*Built with ❤️ for scalable, production-ready hackathon management*
