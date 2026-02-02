# BusWay Backend API

A GIS-powered backend microservice for real-time bus tracking and route management in Morocco (Oujda ...).

## 📋 Overview

BusWay Backend is a Spring Boot REST API that manages bus routes, stops, schedules, and real-time bus tracking with geospatial capabilities. It uses PostGIS for geographic queries and supports all the contrey and we didt speacial for morocco cities like Oujda transit systems.

## 🚀 Key Features

### GIS & Geospatial Capabilities
- **PostGIS Integration**: Store and query geographic data (points, linestrings) with SRID 4326 (WGS84)
- **Nearest Stop/Route Search**: Find stops and routes within a radius from any GPS coordinate
- **Bounding Box Queries**: Query stops/routes within a geographic rectangle
- **Distance Calculations**: Calculate distances between stops and route lengths
- **GeoJSON Export**: Export stops and routes as GeoJSON format for mapping libraries

### Real-Time Tracking
- **Bus Location Updates**: Track bus positions with GPS coordinates
- **Next Stop Prediction**: Track which stop a bus is heading to
- **Approaching Buses**: Find buses approaching a specific stop
- **Occupancy Tracking**: Monitor passenger count on buses

### Schedule Management
- **Timetables**: Manage departure times per route and stop
- **Day-Based Schedules**: Different schedules for different days of the week
- **Upcoming Departures**: Find next departures from any stop

### Advanced Filtering
- **Accessibility**: Filter stops by wheelchair accessibility
- **Facilities**: Filter stops with shelters, benches, or both
- **Zone/City**: Filter by city (Oujda, Algiers) or zone
- **Active Status**: Soft delete support with `is_active` flag

## 🛠️ Tech Stack

### Core Framework
- **Spring Boot 3.2.3** - Main application framework
- **Spring Data JPA** - Database ORM
- **Spring Security** - Authentication & authorization
- **Spring Validation** - Input validation

### Programming Language
- **Java 21** - Modern Java with latest features

### Database
- **PostgreSQL** - Primary database
- **PostGIS 2.5.1** - Geospatial extension for PostgreSQL

### ORM & Spatial
- **Hibernate 6.4.4** - JPA implementation
- **Hibernate Spatial** - Spatial data support
- **JTS Topology Suite 1.18.2** - Geometry operations

### Build Tools
- **Maven** - Dependency management and build tool

### Libraries
- **Lombok 1.18.30** - Reduce boilerplate code
- **Jackson** - JSON serialization/deserialization
- **JWT (jjwt 0.11.5)** - Token-based authentication
- **HikariCP** - Database connection pooling

### Development Tools
- **Spring Boot DevTools** - Hot reload during development

## 📊 Database Schema

### Core Entities

#### Stops
- GPS location (Point geometry)
- Accessibility features (wheelchair, shelter, bench)
- Zone and city classification
- Unique code identifier

#### Routes
- Route geometry (LineString)
- Total distance and estimated duration
- Direction (FORWARD, BACKWARD, BIDIRECTIONAL)
- Color coding for UI

#### Buses
- Real-time GPS location
- Current speed and direction
- Capacity and occupancy
- Status (ACTIVE, INACTIVE, MAINTENANCE)
- Next stop prediction

#### Schedules
- Departure times per route/stop
- Day of week support
- Travel time between stops

#### RouteStops
- Links routes to stops with sequence order
- Estimated travel time between stops

## 🔌 API Endpoints

### Base URL
```
http://localhost:9090/api/busway
```

### Stops API (`/api/busway/stops`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Get all active stops |
| GET | `/{id}` | Get stop by ID |
| GET | `/code/{code}` | Get stop by code |
| GET | `/nearest?lat={lat}&lng={lng}&radius={radius}` | Find stops within radius |
| GET | `/top-nearest?lat={lat}&lng={lng}&limit={limit}` | Get top N nearest stops |
| GET | `/bbox?minLon={}&minLat={}&maxLon={}&maxLat={}` | Find stops in bounding box |
| GET | `/city/{city}` | Get stops by city |
| GET | `/accessible` | Get wheelchair-accessible stops |
| GET | `/with-shelter` | Get stops with shelters |
| GET | `/with-bench` | Get stops with benches |
| GET | `/with-facilities` | Get stops with both facilities |
| GET | `/distance?stopId1={}&stopId2={}` | Calculate distance between stops |
| GET | `/route/{routeId}` | Get stops on a route |
| GET | `/search?q={query}` | Search stops by name |
| GET | `/zone/{zone}` | Get stops by zone |
| GET | `/statistics` | Get stop statistics |
| GET | `/geojson` | Export all stops as GeoJSON |
| GET | `/{id}/geojson` | Export single stop as GeoJSON |
| POST | `/` | Create new stop |
| POST | `/bulk` | Create multiple stops |
| PUT | `/{id}` | Update stop |
| PATCH | `/{id}` | Partial update stop |
| PATCH | `/{id}/location?lat={}&lng={}` | Update stop location |
| PATCH | `/{id}/toggle-status` | Toggle stop active status |
| DELETE | `/{id}` | Soft delete stop |
| DELETE | `/{id}/hard` | Permanently delete stop |
| DELETE | `/bulk?ids={}` | Delete multiple stops |

### Routes API (`/api/busway/routes`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Get all active routes |
| GET | `/{id}` | Get route by ID |
| GET | `/number/{routeNumber}` | Get route by number |
| GET | `/search?q={query}` | Search routes by name |
| GET | `/nearby?lat={lat}&lng={lng}&radius={radius}` | Find routes near location |
| GET | `/bbox?minLon={}&minLat={}&maxLon={}&maxLat={}` | Find routes in bounding box |
| GET | `/{id}/length` | Get route length in km |
| GET | `/{id}/connections` | Get connecting routes |
| POST | `/` | Create new route |
| PUT | `/{id}` | Update route |
| DELETE | `/{id}` | Soft delete route |
| DELETE | `/{id}/hard` | Permanently delete route |

### Buses API (`/api/busway/buses`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Get all active buses |
| GET | `/{id}` | Get bus by ID |
| GET | `/number/{busNumber}` | Get bus by number |
| GET | `/nearest?lat={lat}&lng={lng}&radius={radius}` | Find buses near location |
| GET | `/top-nearest?lat={lat}&lng={lng}&limit={limit}` | Get top N nearest buses |
| GET | `/route/{routeId}` | Get buses on a route |
| GET | `/heading-to-stop/{stopId}` | Get buses heading to a stop |
| GET | `/approaching/{stopId}` | Get buses approaching a stop |
| POST | `/` | Create new bus |
| PUT | `/{id}` | Update bus |
| PUT | `/{id}/location?lat={}&lng={}&speed={}&direction={}` | Update bus location |
| PUT | `/{id}/occupancy?passengers={}` | Update bus occupancy |
| PUT | `/{id}/next-stop/{stopId}` | Update bus next stop |
| PUT | `/{id}/status?status={}` | Change bus status |
| DELETE | `/{id}` | Delete bus |

### Schedules API (`/api/busway/schedules`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/route/{routeId}` | Get schedules for a route |
| GET | `/stop/{stopId}` | Get schedules for a stop |
| GET | `/route/{routeId}/day/{day}` | Get schedules by route and day |
| GET | `/upcoming?stopId={}&dayOfWeek={}&currentTime={}` | Get upcoming departures |
| GET | `/between-stops?routeId={}&fromStopId={}&toStopId={}` | Get schedule between stops |
| POST | `/` | Create new schedule |
| PUT | `/{id}` | Update schedule |
| DELETE | `/{id}` | Delete schedule |

## 🏗️ Architecture

### Layered Architecture
```
┌─────────────────────────────────────┐
│         Controllers (REST API)       │
├─────────────────────────────────────┤
│            Services (Business)      │
├─────────────────────────────────────┤
│          Repositories (Data)        │
├─────────────────────────────────────┤
│         JPA/Hibernate (ORM)         │
├─────────────────────────────────────┤
│   PostgreSQL + PostGIS (Database)   │
└─────────────────────────────────────┘
```

### Key Design Patterns
- **DTO Pattern**: Separate request/response objects from entities
- **Service Layer**: Business logic encapsulated in service classes
- **Repository Pattern**: Data access abstraction via Spring Data JPA
- **Soft Delete**: Records marked inactive instead of permanent deletion
- **GeoJSON Support**: Geographic data export for mapping libraries

## 🗺️ Supported Cities

### Algiers (الجزائر العاصمة)
- Route 1: وسط المدينة → الولاية → سيدي يحيى
- Route 1B: وسط المدينة → سهب الخيل

### Oujda (وجدة)
- Route U1: Centre Ville → Université Mohammed Premier (20 stops)

## 📝 Setup Instructions

### Prerequisites
- **Java 21** (JDK 21 or higher)
- **PostgreSQL 12+** with PostGIS extension
- **Maven 3.8+**

### Database Setup
```sql
-- Create database
CREATE DATABASE busway_db;

-- Connect to database
\c busway_db

-- Enable PostGIS extension
CREATE EXTENSION IF NOT EXISTS postgis;
```

### Configuration
Edit `src/main/resources/application.properties`:
```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/busway_db
spring.datasource.username=postgres
spring.datasource.password=your_password
```

### Running the Application
```bash
# Using Maven
mvn spring-boot:run

# Or build and run
mvn clean package
java -jar target/busway-backend-1.0.0.jar
```

The API will start on `http://localhost:9090`

### Data Seeding
On first startup, the application automatically:
1. Creates database tables via Hibernate (`ddl-auto=update`)
2. Runs the SQL migration script (`V2__insert_bus_routes.sql`)
3. Executes `BusRouteSeeder` for Algiers routes

## 🔐 Authentication

The API uses JWT (JSON Web Tokens) for authentication via Spring Security.

### Public Endpoints
- `/api/test`
- `/api/buses/nearest`
- `/api/stops/nearest`

### Protected Endpoints
All other endpoints require authentication via JWT token.

## 📊 API Response Format

All endpoints return a standardized response:

```json
{
  "success": true,
  "message": "Operation successful",
  "data": { ... },
  "timestamp": "2026-05-13T08:00:00"
}
```

## 🌐 CORS Configuration

Cross-Origin Resource Sharing is configured for:
- `http://localhost:3000` (React)
- `http://localhost:5173` (Vite)

## 🧪 Testing

Run tests with:
```bash
mvn test
```

## 📦 Project Structure

```
busway/
├── src/
│   ├── main/
│   │   ├── java/com/busway/
│   │   │   ├── entity/          # JPA entities
│   │   │   ├── controller/      # REST controllers
│   │   │   ├── service/         # Business logic
│   │   │   ├── repository/      # Data access
│   │   │   ├── dto/             # Request/Response DTOs
│   │   │   ├── config/          # Configuration
│   │   │   ├── exception/       # Global exception handling
│   │   │   ├── seeder/          # Data seeding
│   │   │   └── util/            # Utilities
│   │   └── resources/
│   │       ├── application.properties
│   │       └── db/migration/    # SQL migrations
└── pom.xml
```

## 🔧 Special Features Explained

### PostGIS Geometry Types
- **Point**: GPS coordinates (lat, lng) for stops and bus locations
- **LineString**: Route paths as connected line segments
- **SRID 4326**: WGS84 coordinate system (standard GPS)

### Spatial Queries
```sql
-- Find stops within 500m radius
SELECT * FROM stops 
WHERE ST_DWithin(location, ST_SetSRID(ST_MakePoint(-1.9, 34.6), 4326), 500);

-- Calculate distance between two stops
SELECT ST_Distance(s1.location, s2.location) 
FROM stops s1, stops s2 
WHERE s1.id = 1 AND s2.id = 2;
```

### GeoJSON Export
Export stops in GeoJSON format for mapping libraries like Leaflet, Mapbox, or Google Maps:
```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [-1.9070, 34.6810]
      },
      "properties": {
        "name": "Centre Ville",
        "code": "OUJ001"
      }
    }
  ]
}
```

## 📈 Performance Optimizations

- **HikariCP**: High-performance JDBC connection pool
- **Batch Processing**: Configured batch size for bulk operations
- **Lazy Loading**: JPA lazy loading for relationships
- **Indexing**: Unique indexes on codes and route numbers
- **Connection Pooling**: Optimized pool size (5-20 connections)

## 🐛 Known Issues

- **Java Version**: System must use Java 21 (Java 8 will not compile)
- **PostGIS Required**: Database must have PostGIS extension enabled
- **Flyway Not Used**: SQL scripts run via Spring Boot data initialization

## 📄 License

This project is part of the BusWay application suite.

## 👥 Team

BusWay Development Team

---

**Version**: 1.0.0  
**Last Updated**: May 2026  
**Status**: Active Development
