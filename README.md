# IN2Flame - European Emergency Services Command & Control System

A mission-critical command-and-control system for wildfire emergency services across European and North African countries. Features real-time satellite fire detection (NASA FIRMS, EFFIS, EUMETSAT MTG), GIS mapping, AI-powered fire spread prediction, and comprehensive ground operations management.

**Designed and developed by Tomislav Vrbicic**

![IN2Flame Dashboard](https://img.shields.io/badge/Version-2.0-blue) ![License](https://img.shields.io/badge/License-MIT-green) ![Node](https://img.shields.io/badge/Node-20+-brightgreen) ![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue)

## Features

- **Real-time Satellite Fire Detection** - Integration with NASA FIRMS, EFFIS, and EUMETSAT MTG Active Fire Monitoring
- **173 Weather Monitoring Stations** - Live weather data across all EU member states + North African countries
- **27 AI/ML Analysis Models** - Fire spread prediction, risk assessment, resource optimization
- **GIS Mapping** - Interactive MapLibre-based tactical map with multiple layers
- **Emergency Resource Dispatch** - Fire trucks, ambulances, rescue teams, civil protection, police, HGSS
- **Evacuation Management** - Population-based evacuation planning with routing
- **GEMS Event System** - Centralized event monitoring with severity-based alerts
- **PDF Report Generation** - Automated mission reports and daily briefings
- **Multi-language Support** - English (default), with extensible i18n

## System Requirements

### Minimum Requirements
- **Node.js** 20.x or higher
- **Python** 3.11+ (for ML inference services)
- **PostgreSQL** 14+ database
- **RAM:** 4GB minimum (8GB recommended)
- **Modern web browser** (Chrome, Firefox, Edge, Safari)

### Optional Dependencies
- CUDA-enabled GPU for accelerated ML inference
- Redis for session caching (production)

---

## Installation

### Step 1: Clone the Repository

```bash
git clone https://github.com/your-username/in2flame.git
cd in2flame
```

### Step 2: Install Node.js Dependencies

```bash
npm install
```

### Step 3: Install Python Dependencies

The ML inference service requires Python packages:

```bash
pip install flask flask-cors numpy scikit-learn torch xgboost h5py netcdf4 requests joblib
```

Or using a virtual environment (recommended):

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install flask flask-cors numpy scikit-learn torch xgboost h5py netcdf4 requests joblib
```

### Step 4: Set Up PostgreSQL Database

Create a PostgreSQL database:

```bash
# Using psql
createdb in2flame

# Or using PostgreSQL command line
psql -U postgres -c "CREATE DATABASE in2flame;"
```

### Step 5: Configure Environment Variables

Create a `.env` file in the root directory:

```env
# ===========================================
# DATABASE CONFIGURATION (Required)
# ===========================================
DATABASE_URL=postgresql://username:password@localhost:5432/fire_c2
PGHOST=localhost
PGPORT=5432
PGUSER=username
PGPASSWORD=password
PGDATABASE=fire_c2

# ===========================================
# SESSION SECURITY (Required)
# ===========================================
# Generate a secure random string (min 32 characters)
SESSION_SECRET=your-secure-random-string-here-min-32-chars

# ===========================================
# NASA FIRMS API (Required for live fire detection)
# ===========================================
# Get your free API key from: https://firms.modaps.eosdis.nasa.gov/api/area/
NASA_FIRMS_API_KEY=your-nasa-firms-api-key

# ===========================================
# EUMETSAT API (Optional - for satellite products)
# ===========================================
# Register at: https://data.eumetsat.int/
EUMETSAT_CONSUMER_KEY=your-eumetsat-key
EUMETSAT_CONSUMER_SECRET=your-eumetsat-secret

# ===========================================
# OpenSky Network (Optional - for aircraft tracking)
# ===========================================
OPENSKY_CLIENT_ID=your-opensky-client-id
OPENSKY_CLIENT_SECRET=your-opensky-client-secret
```

### Step 6: Initialize the Database

Push the database schema:

```bash
npm run db:push
```

### Step 7: Start the Application

**Development mode** (with hot reload):

```bash
npm run dev
```

**Production mode:**

```bash
npm run build
npm start
```

The application will be available at **http://localhost:5000**

---

## Docker Deployment (Optional)

Create a `Dockerfile` in the root directory:

```dockerfile
FROM node:20-alpine

WORKDIR /app

# Install Python for ML services
RUN apk add --no-cache python3 py3-pip

# Copy package files
COPY package*.json ./

# Install Node dependencies
RUN npm ci --only=production

# Install Python dependencies
RUN pip3 install flask flask-cors numpy scikit-learn torch xgboost requests joblib

# Copy application code
COPY . .

# Build the application
RUN npm run build

# Expose port
EXPOSE 5000

# Start the application
CMD ["npm", "start"]
```

Build and run:

```bash
docker build -t in2flame .
docker run -p 5000:5000 --env-file .env in2flame
```

---

## API Keys Setup

### NASA FIRMS API Key (Required)
1. Visit [NASA FIRMS](https://firms.modaps.eosdis.nasa.gov/api/area/)
2. Register for a free account
3. Request an API key
4. Add to your `.env` file as `NASA_FIRMS_API_KEY`

### EUMETSAT API (Optional)
1. Register at [EUMETSAT Data Store](https://data.eumetsat.int/)
2. Create API credentials
3. Add consumer key and secret to `.env`

### Open-Meteo Weather API
**No API key required** - The system uses the free Open-Meteo API for weather data.

---

## Project Structure

```
in2flame/
├── client/                 # Frontend React application
│   ├── src/
│   │   ├── components/     # UI components
│   │   ├── hooks/          # Custom React hooks
│   │   ├── lib/            # Utility functions
│   │   └── pages/          # Page components
├── server/                 # Backend Express server
│   ├── routes.ts           # API routes
│   ├── storage.ts          # Database operations
│   ├── weather-service.ts  # Weather API integration
│   ├── ml-inference.py     # ML model serving
│   └── eumetsat-hdf5-service.py  # Satellite data processing
├── shared/                 # Shared types and schemas
│   └── schema.ts           # Database schema (Drizzle ORM)
├── ml-models/              # Trained ML models
└── attached_assets/        # Static assets
```

---

## Configuration

### Map Tile Providers

The system uses MapLibre GL with configurable tile sources. Edit `client/src/components/map-view.tsx` to change map providers.

### Weather Monitoring Locations

Weather stations are configured in `server/weather-service.ts`. The default configuration includes 173 locations across:
- All 27 EU member states
- Additional European countries (UK, Norway, Switzerland, etc.)
- North African countries (Morocco, Algeria, Tunisia, Libya, Egypt)

### ML Models

Pre-trained models are stored in `ml-models/` directory:
- Fire Spread Prediction (XGBoost)
- Fire Risk Assessment (Random Forest)
- Fire Ignition Probability (Neural Network)
- Smoke Dispersion (Physics-based + ML)
- Resource Optimization (DQN)
- Satellite Image Analysis (CNN)

---

## Usage

### Creating an Admin Account

After starting the application, register a new account through the web interface at `http://localhost:5000/auth`.

### Operational Modes

1. **Live Data Mode** - Real-time data from satellite and weather APIs
2. **Simulation Mode** - Training scenarios with simulated fire events

### Panel System

The application uses a dynamic panel system. Available panels:
- National Overview
- Tactical Fire Scene
- Weather Dashboard
- GEMS Events
- Emergency Resources
- Evacuation Population
- Navigation & Routing
- EUMETSAT Products
- Reports Generation

---

## Development

### Running in Development Mode

```bash
npm run dev
```

### Database Operations

```bash
# Generate migration files
npm run db:generate

# Push schema changes to database
npm run db:push

# Open Drizzle Studio (database GUI)
npm run db:studio
```

### Type Checking

```bash
npx tsc --noEmit
```

---

## Troubleshooting

### Common Issues

1. **Database connection failed**
   - Verify PostgreSQL is running: `pg_isready`
   - Check DATABASE_URL format: `postgresql://user:password@host:port/database`
   - Ensure database exists: `psql -l`

2. **ML models not loading**
   - Install Python dependencies: `pip install -r requirements.txt`
   - Check `ml-models/` directory exists
   - Verify Python 3.11+ is installed: `python --version`

3. **Weather data not loading**
   - Check internet connectivity
   - Open-Meteo API may have rate limits (wait and retry)
   - Verify no firewall blocking outbound requests

4. **Map not displaying**
   - Check browser console for tile loading errors
   - Verify MapLibre GL JS is loaded
   - Check Content Security Policy settings

5. **Port 5000 already in use**
   ```bash
   # Find process using port 5000
   lsof -i :5000
   # Kill the process
   kill -9 <PID>
   ```

---

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Code Style

- Use TypeScript for all new code
- Follow existing ESLint configuration
- Run `npm run lint` before committing
- Add tests for new features

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## Acknowledgments

- NASA FIRMS for fire detection data
- EUMETSAT for satellite products
- Open-Meteo for weather API
- MapLibre for mapping library
- Shadcn/ui for UI components

---



---

**Note:** All rights reserved by Tomislav Vrbicic
