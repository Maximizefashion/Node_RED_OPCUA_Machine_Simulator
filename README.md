# Node_RED_Machine_Simulator

# OPC-UA Machine Simulator User Guide

## Quick Start

**Environment**: Raspberry Pi 5, Debian 12, Node-RED 4.0.9, node-red-contrib-opcua 0.2.340

**Initial Setup**: Deploy the flow - three configuration nodes automatically initialize and start the simulation.

**Connection**: OPC-UA clients connect to `opc.tcp://localhost:4840` (Anonymous/None security)

## Core Controls

### Simulation Control
- **Start Simulation**: Begins production cycles with current settings
- **Cycle Time**: 500ms, 1000ms, 2000ms, or custom timing via inject buttons

### Machine Status Control
- **Enable/Disable Automation**: Toggle automatic random downtime events
- **Manual Status Reason**: Set 0 (clear), 101, 102, 103, or custom values
- **Override Behavior**: Any manual status injection automatically disables automation

## Production Counter Management

### Count Modes (ProdCount & ScrapCount)
**Incremental Mode** (default):
- **+1**: Standard increment
- **+10**: Fast increment  
- **Random 1-100**: Variable increment each cycle
- **Custom**: Set any increment value

**Absolute Mode**:
- **Range Control**: Configure min/max values (default: 1-10)
- **Random Generation**: New random value each cycle within range

### Rollover Protection
- **Individual Control**: Set prodCount and scrapCount rollovers independently
- **Bulk Control**: Set both rollovers to same value
- **Auto-Reset**: Counters automatically reset to 0 if rollover set below current count

### Reset Functions
- **Reset ProdCount**: Reset production count to 0
- **Reset ScrapCount**: Reset scrap count to 0
- **Independent Operation**: Each counter resets individually

## Bidirectional Variables

### readWriteInt (Integer Variable)
**Node-RED Controls**:
- **Set Default Value 100**: Initialize with default
- **Manual Write Value**: Send 150 (or edit for custom value)

**UAExpert Integration**:
- Write any integer value from UAExpert
- Real-time monitoring displays external writes with timestamps
- Color-coded visual feedback (orange for external writes)

### readWriteString (String Variable)
**Node-RED Controls**:
- **Set Default String 'Hello'**: Initialize with default
- **Manual Write String**: Send "Node-RED Message" (or edit for custom text)

**UAExpert Integration**:
- Write any string value from UAExpert
- String truncation for display (20 characters + "...")
- Real-time monitoring with full logging

## Real-Time Monitoring

### Status Displays
Each monitor shows real-time information with color coding:
- **Blue**: Production counts and configuration
- **Orange**: Scrap counts and reasons
- **Green**: Equipment running status
- **Red**: Equipment down status
- **Yellow**: Scrap reason codes
- **Purple**: Status reason codes

### External Write Detection
- **Single Client Architecture**: One OPC-UA client monitors both variables
- **Smart Routing**: Automatically routes data based on variable type
- **Real-Time Feedback**: Immediate status updates for external writes
- **Debug Logging**: Complete write operation logs in debug panel

## Advanced Configuration

### Scrap & Downtime Codes
Edit function nodes to customize:
- **Scrap Codes**: Default [40, 50, 60, 70, 80, 90, 100]
- **Downtime Codes**: Default [101, 102, 103]
- **Weights**: Equal probability (configurable)

### Timing & Probability
Configure via "Set Config" function:
- **Scrap Probability**: Default 10% (0.1)
- **Downtime Probability**: Default 2% (0.02)
- **Downtime Duration**: 5-15 seconds

### Count Range Settings
For absolute mode operation:
- **ProdCount Range**: Default 1-20 (configurable)
- **ScrapCount Range**: Default 1-15 (configurable)

## OPC-UA Technical Details

### Server Configuration
- **Port**: 4840
- **Namespace**: 1 (ns=1)
- **Security**: Anonymous/None for development
- **Variable Creation**: Automatic on flow startup

### Variable Message Format
OPC-UA server updates use this structure:
```json
{
  "messageType": "Variable",
  "namespace": "1", 
  "variableName": "prodCount",
  "datatype": "Int32",
  "variableValue": 1547
}
```

### Client Subscription Setup
For external OPC-UA clients:
1. **Endpoint**: opc.tcp://localhost:4840
2. **Security Policy**: None
3. **Security Mode**: None  
4. **Authentication**: Anonymous
5. **Subscribe** to desired NodeIds (ns=1;s=variableName)

## Ignition Integration

### Tag Import
All variables follow standard manufacturing conventions:
- **equipStatus**: Drive production line graphics (0=Down, 1=Running)
- **prodCount/scrapCount**: Production tracking with advanced counting modes
- **statusReason**: Downtime analysis (0=Running, 101-103=Maintenance)
- **scrapReason**: Quality analysis (40-100=Defect codes)

### Bidirectional Testing
- **readWriteInt/readWriteString**: Test operator interface functionality
- **Real-time Feedback**: Verify HMI write operations
- **Data Validation**: Monitor value changes in both directions

## Troubleshooting

### Common Issues
- **No External Writes Detected**: Check UAExpert connection to correct endpoint
- **Automation Not Working**: Verify automation is enabled via toggle button
- **Counter Resets Unexpectedly**: Check rollover settings vs current count values

### Debug Features
- **Monitor Nodes**: Real-time status display for all variables
- **Debug Logs**: External write detection with timestamps
- **Visual Feedback**: Color-coded status updates for all operations 
