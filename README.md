# Guild Awards

A fun and interactive React + Python application that analyzes your World of Warcraft guild's combat logs to celebrate your members' achievements, quirks, and memorable moments.

## 🎯 Overview

Guild Awards parses your Warcraft Logs data to generate entertaining and insightful awards for your guild members. Whether it's celebrating your top performers or playfully highlighting those special "achievement moments," this app brings a fresh perspective to your raid statistics.

## ✨ Features

### 🏆 Performance Awards

- **Damage Dealer Supreme** - Highest overall DPS across all encounters
- **Healing Virtuoso** - Most effective healer
- **Tank Titan** - Best damage mitigation and survival
- **Utility Master** - Most crowd control and utility spell usage

### 🎭 Specialty Awards

- **The Dispel Champion** - Most debuffs cleansed
- **Interrupt Specialist** - Highest number of successful interrupts
- **Battle Resurrector** - Most combat resurrections performed
- **Avoidance Ace** - Best at dodging mechanics

### 😄 Fun Awards

- **Floor Inspector** - Most deaths to standing in fire
- **Gravity Challenger** - Most deaths from falling
- **Potion Hoarder** - Least consumables used
- **Marathon Runner** - Most distance traveled during fights
- **Last One Standing** - Most times being the final survivor
- **Speedrunner** - First to die to new mechanics
- **Social Butterfly** - Most deaths while typing in chat

### 📊 Visualizations

- Interactive performance trends
- Raid progression timelines
- Player improvement graphs
- Comparative analysis between raid nights

## 🛠 Technical Stack

### Frontend

- React.js
- TypeScript
- Tailwind CSS
- Chart.js/D3.js for visualizations

### Backend

- Node.js
- Express
- GraphQL integration with Warcraft Logs API

## 📝 Requirements

- Warcraft Logs API Client ID and Secret
- Guild logs must be available on Warcraft Logs
- Modern web browser for optimal experience

## 🚀 Getting Started

1. Clone the repository
2. Install dependencies
3. Configure environment variables
4. Set up Warcraft Logs API credentials
5. Run the development server

## 🔑 API Configuration

The application uses the Warcraft Logs API v2 with OAuth 2.0 authentication. You'll need to:

1. Create a client on Warcraft Logs
2. Configure OAuth credentials
3. Set up proper authorization flows

## 🔧 Data Processing Architecture

### Data Flow

1. **Data Fetching (GraphQL)**

```python
from gql import gql, Client
from gql.transport.requests import RequestsHTTPTransport

# Example GraphQL query for fight data
query = gql('''
    query getFightData($guildID: String!, $startTime: Float!, $endTime: Float!) {
        reportData {
            reports(guildID: $guildID, startTime: $startTime, endTime: $endTime) {
                data {
                    fights {
                        id
                        startTime
                        endTime
                        encounterID
                        fightPercentage
                        lastPhaseForPercentageDisplay
                    }
                }
            }
        }
    }
''')
```

2. **Data Processing (Pandas)**

```python
import pandas as pd

class LogProcessor:
    def __init__(self):
        self.fights_df = None
        self.players_df = None
        self.events_df = None

    def process_fight_data(self, raw_data):
        # Convert GraphQL response to DataFrame
        self.fights_df = pd.DataFrame(raw_data['reportData']['reports']['data'])
        
        # Add time-based calculations
        self.fights_df['duration'] = self.fights_df['endTime'] - self.fights_df['startTime']
        
        # Normalize metrics
        self.fights_df['normalized_damage'] = self.fights_df.groupby('encounterID')['damage'].transform(
            lambda x: (x - x.mean()) / x.std()
        )

    def calculate_awards(self):
        # Example: Calculate DPS rankings
        dps_awards = self.fights_df.groupby('playerID').agg({
            'damage': 'sum',
            'duration': 'sum'
        }).assign(
            dps = lambda x: x['damage'] / x['duration']
        ).sort_values('dps', ascending=False)

        return dps_awards
```

## 🧮 Award Calculations

### Performance Awards

#### Damage Dealer Supreme

```graphql
// Query all fights in a given timeframe
// For each player:
DPS_Score = SUM(total_damage_done) / SUM(fight_duration)
Normalized_DPS = DPS_Score / AVERAGE(raid_dps)
```

#### Healing Virtuoso

```graphql
Healing_Score = (
    (effective_healing + absorbs) -
    (overhealing * 0.5)
) / fight_duration
```

#### Tank Titan

```graphql
Tank_Score = (
    damage_taken_reduced +
    (successful_active_mitigation * 2) +
    (survival_during_tank_busters * 3)
) / fight_duration
```

### Specialty Awards

#### The Dispel Champion

```graphql
Dispel_Score = (
    (successful_dispels * 1.5) +
    (critical_dispels * 3)
) / fights_participated
```

#### Interrupt Specialist

```graphql
Interrupt_Score = (
    (successful_interrupts * 2) +
    (critical_interrupts * 3)
) / opportunities_to_interrupt
```

### Fun Awards

#### Floor Inspector & Gravity Challenger

```graphql
Floor_Score = (
    (deaths_to_fire * 2) +
    (deaths_to_void_zones * 2) +
    (deaths_to_obvious_mechanics * 3)
) / fights_participated
```

### Data Processing

#### Normalization Factors

```typescript
// Normalize scores across different fight lengths
normalized_score = raw_score * (base_fight_duration / actual_fight_duration)

// Adjust for raid size
size_coefficient = 20 / actual_raid_size

// Role-based normalization
role_weights = {
    'tank': 1.2,
    'healer': 1.0,
    'dps': 0.8
}
```

#### Statistical Significance

```typescript
confidence_threshold = 0.95
minimum_sample_size = 5

// Z-score calculation for outlier detection
z_score = (value - mean) / standard_deviation
is_significant = Math.abs(z_score) > 1.96  // 95% confidence interval
```

## 🤝 Contributing

We welcome contributions! Please see our contributing guidelines for more details.

## 📜 License

MIT License

## 🙏 Acknowledgments

- Warcraft Logs for providing the comprehensive API
- The WoW community for inspiration
- All contributors and testers

## 📁 Project Structure

```text
guild_awards/
├── frontend/ # React frontend application
│ ├── public/ # Static files
│ ├── src/
│ │ ├── components/ # React components
│ │ │ ├── awards/ # Award-specific components
│ │ │ │ ├── Performance.tsx
│ │ │ │ ├── Specialty.tsx
│ │ │ │ └── Fun.tsx
│ │ │ ├── visualizations/ # Data visualization components
│ │ │ │ ├── Timeline.tsx
│ │ │ │ └── Trends.tsx
│ │ │ └── common/ # Shared components
│ │ ├── hooks/ # Custom React hooks
│ │ ├── services/ # API services
│ │ ├── types/ # TypeScript type definitions
│ │ └── utils/ # Utility functions
│ ├── package.json
│ └── tsconfig.json
│
├── backend/ # Python backend application
│ ├── api/ # API endpoints
│ │ ├── init.py
│ │ ├── auth.py # Authentication routes
│ │ └── awards.py # Award calculation routes
│ │
│ ├── core/ # Core business logic
│ │ ├── init.py
│ │ ├── processors/ # Data processing modules
│ │ │ ├── init.py
│ │ │ ├── log_processor.py
│ │ │ └── award_calculator.py
│ │ │
│ │ └── models/ # Data models
│ │ ├── init.py
│ │ ├── fight.py
│ │ └── player.py
│ │
│ ├── services/ # External service integrations
│ │ ├── init.py
│ │ └── warcraft_logs.py # Warcraft Logs API client
│ │
│ ├── utils/ # Utility functions
│ │ ├── init.py
│ │ └── calculations.py
│ │
│ ├── config.py # Configuration settings
│ ├── requirements.txt # Python dependencies
│ └── main.py # Application entry point
│
├── docker/ # Docker configuration
│ ├── frontend.Dockerfile
│ └── backend.Dockerfile
│
├── tests/ # Test suites
│ ├── frontend/
│ └── backend/
│
├── .env.example # Example environment variables
├── docker-compose.yml # Docker compose configuration
├── README.md # Project documentation
└── .gitignore # Git ignore rules
```

This structure follows best practices for a modern web application:

1. **Clear Separation**: Frontend and backend are clearly separated
2. **Modular Design**: Components and functionality are organized into logical modules
3. **Scalable**: Easy to add new features and components
4. **Maintainable**: Clear organization makes it easy to find and fix issues
5. **Testable**: Dedicated test directories for both frontend and backend
6. **Deployable**: Docker configuration included for containerization
