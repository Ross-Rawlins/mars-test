# Mars Rover Control System

A distributed system for controlling Mars rovers with Earth-based command submission and real-time position tracking.

## 🚀 Overview

This system addresses the unique challenges of Mars rover control, including communication delays, limited battery life, and the high cost of hardware deployment. The architecture supports scalable command queuing and robust failure detection.

## 🌌 Mars Mission Constraints

### Communication
- **Signal delay**: ~12.5 minutes average between Earth and Mars
- **Real-time control**: Impossible due to communication delays
- **Command confirmation**: GPS-based position verification

### Hardware Limitations
- **Deployment cost**: $130-$400 per kilogram to Mars
- **Average rover weight**: ~600kg (excluding small rovers like Sojourner)
- **No magnetic compass**: Traditional NESW navigation unavailable
- **2D navigation only**: No Z-axis/vertical movement considered
- **Fixed dimensions**: Robot size matches grid cell size

### Environment
- **No obstacle detection**: Clear path assumption
- **Energy critical**: Battery management essential for mission success

## 📊 System Specifications

### Direction System
- **Range**: -180° to +180° (360° coverage)
- **No compass dependency**: Degree-based navigation system

### Movement Commands
| Command | Action | Value |
|---------|--------|-------|
| `R` | Turn Right | +90° |
| `L` | Turn Left | -90° |
| `F` | Move Forward | 1 Grid Unit |

### Battery Management
- **Capacity**: 0-100 units
- **Consumption**: 1 unit per grid movement
- **Recharge time**: 24 hours (0→100)
- **Pre-movement validation**: Required before each command

## 🏗️ Architecture

### Server-Side Components

#### Command Submission
```javascript
submitCommands(
  startPosition: {x: number, y: number}, 
  gridSize: {x: number, y: number}, 
  robotId: string,
  movementPlan: Array<string>
): commandId
```

**Process Flow:**
1. Validate movement plan
2. Get current robot position
3. Validate grid boundaries
4. Submit command to queue if valid
5. Return command ID for tracking

#### Grid Validation
```javascript
validateGrid(
  gridSize: {x: number, y: number},
  currentPosition: {x: number, y: number}
): boolean
```

Checks for:
- Boundary violations
- Overlap with known failure zones
- Historical data conflicts

#### Movement Plan Validation
```javascript
validateMovementPlan(
  movementPlan: Array<string>,
  gridSize: {x: number, y: number}
): {boolean, message}
```

**Validation Rules:**
- Maximum 100 characters per plan
- Grid coverage efficiency analysis
- Battery requirement assessment

#### Command Monitoring
```javascript
listenForCommandResult(commandId): Promise<Result>
getCommandResult(commandId): CommandResult
```

**Features:**
- WebSocket or polling-based updates
- Timeout detection based on expected completion time
- Automatic failure flagging for overdue commands

### Robot-Side Components

#### Command Processing
```javascript
listenForMovementCommands(
  startPosition: {x: number, y: number, direction: number},
  gridSize: {x: number, y: number},
  movementPlan: Array<string>
)
```

**Execution Flow:**
1. Validate battery sufficiency
2. Process movement commands sequentially
3. Report position after each move
4. Handle movement failures gracefully

#### Battery Management
```javascript
getCurrentBatteryLevel(): number
validateBatteryUsage(movementPlan: Array<string>): boolean
listenForBatteryChanges(): Listener<number>
```

#### Position Reporting
```javascript
robotMoved(
  commandId: string,
  currentPosition: {x: number, y: number},
  currentDirection: number,
  movementPlanIndex: number,
  movementPlanLength: number,
  currentBatteryLevel: number
)
```

## 🔧 Implementation Guidelines

### Scalability Considerations
- **Queue-based architecture**: Support multiple concurrent rovers
- **Batch processing**: Start with small areas, scale gradually
- **Database design**: Relational DB for failure tracking and position history

### Error Handling
- **Communication failures**: Store last known position
- **Battery depletion**: Return current battery level and ETA for sufficient charge
- **Grid violations**: Log and return detailed error messages
- **Timeout handling**: Mark commands as LOST after expected completion time

### Performance Optimization
- **Efficient pathfinding**: AI-assisted movement plan generation
- **Reduced command overhead**: Minimize communication frequency
- **Smart batching**: Group commands to reduce transmission costs

## 📈 Future Enhancements

- Real-time battery monitoring
- AI-powered path optimization
- Multi-rover coordination
- 3D navigation support
- Obstacle detection integration

## 🛠️ Development Setup

```bash
# Clone the repository
git clone [repository-url]

# Install dependencies
npm install

# Configure Mars communication parameters
cp config.example.js config.js

# Start the server
npm run start
```

## 📝 API Documentation

Detailed API documentation and examples will be provided in the `/docs` directory.

## 🤝 Contributing

Please read our contributing guidelines and ensure all Mars mission constraints are considered in your implementations.

## 📄 License

[Your chosen license]

---

**Mission Critical**: Remember that every command sent to Mars costs significant resources. Test thoroughly on Earth before deployment.
