# VISCA Camera Feedback Implementation - Project Reference Document

## Project Overview

**Project Name:** VISCA Camera Power Status Feedback Implementation  
**Date:** July 1, 2025  
**Version:** 1.0  

## Project Description

This document details the implementation of power status feedback functionality for a VISCA-compatible PTZ camera control module in Crestron SIMPL+. The project involved modifying an existing VISCA camera control module to add real-time power status monitoring through serial communication.

## Original Module Analysis

The original `VISCA_Camera_v1.usp` module provided control functionality for VISCA-compatible cameras with:

- Digital inputs for camera movement (pan/tilt), zoom, preset recall/set
- Special functions (factory reset, firmware retrieval, mirror/flip, menu control)
- Serial command output formatted per the VISCA protocol
- No feedback implementation (one-way communication only)

## Implementation Details

### Added Functionality

1. **Power Status Feedback**
   - Added a digital output (`PowerIsOn`) to indicate camera power state
   - Implemented serial response parsing to detect power status messages
   - Added automatic polling for power status every 10 seconds
   - Added manual query capability via a digital input

### Key Code Components

1. **New I/O Signals**
   ```simplplus
   DIGITAL_INPUT QueryPowerStatus;  // Manual query trigger
   DIGITAL_OUTPUT PowerIsOn;        // Feedback signal
   ```

2. **Response Parsing**
   ```simplplus
   CHANGE rx$
   {
       // Local variable declarations at top of function
       string response[100];
       integer endPos;
       
       // Buffer management for incoming data
       rxBuffer = rxBuffer + rx$;
       
       // Process complete messages (ending with 0xFF)
       while(find(rxBuffer, "\xFF") > 0)
       {
           // Extract response and update buffer
           endPos = find(rxBuffer, "\xFF");
           response = mid(rxBuffer, 1, endPos);
           rxBuffer = right(rxBuffer, len(rxBuffer) - endPos);
           
           // Check for power status response
           if(len(response) >= 4 && byte(response, 2) = 0x50)
           {
               if(byte(response, 3) = 0x02)      // Power ON
                   PowerIsOn = 1;
               else if(byte(response, 3) = 0x03) // Power OFF
                   PowerIsOn = 0;
           }
       }
   }
   ```

3. **Periodic Status Polling**
   ```simplplus
   #DEFINE_CONSTANT POLL_INTERVAL 10  // Poll every 10 seconds
   
   EVENT
   {
       pollCounter = pollCounter + 1;
       
       if(pollCounter >= POLL_INTERVAL)
       {
           makeString(tx$,"%s%s",chr(128+Id),"\x09\x04\x00\xFF");
           pollCounter = 0;
       }
   }
   ```

## Errors Encountered & Solutions

### 1. Timeline Function Errors

**Error:**
```
Error 1001 - Undefined variable: 'TIMELINE_EVENT'
Error 1001 - Undefined variable: 'TIMELINE_CREATE'
```

**Solution:**
- SIMPL+ doesn't have timeline functions like some other languages
- Implemented a counter-based approach using the built-in EVENT handler
- Used a global counter that increments with each EVENT call

### 2. Statement Outside Function Scope

**Error:**
```
Error 1100 - Statement outside of function scope
```

**Solution:**
- Moved the `#define` directive to the compiler directives section
- Changed to proper SIMPL+ syntax: `#DEFINE_CONSTANT` instead of `#define`
- Placed at the top of the file with other compiler directives

### 3. Local Variable Declaration Errors

**Error:**
```
Error 1304 - Local variables must be declared at top of function
```

**Solution:**
- Moved all local variable declarations to the beginning of the function
- SIMPL+ requires all variables to be declared before any executable statements
- Different from C/C++ where variables can be declared anywhere in a code block

## VISCA Protocol Details

### Power Status Query Command
```
8x 09 04 00 FF  (where x is camera address)
```

### Power Status Response Format
- **Power ON:** `y0 50 02 FF` (where y is camera address)
- **Power OFF:** `y0 50 03 FF`

## Best Practices for SIMPL+ Development

1. **Compiler Directives**
   - Place all compiler directives at the top of the file
   - Use `#DEFINE_CONSTANT` for constants, not C-style `#define`

2. **Variable Declarations**
   - Declare all local variables at the beginning of functions
   - Initialize global variables in the `Main()` function

3. **Serial Communication**
   - Always implement proper buffering for incoming serial data
   - Use `CHANGE` handlers to process incoming data
   - Check for complete messages with proper termination

4. **Event Handling**
   - Use the built-in `EVENT` handler for periodic tasks
   - Implement counter-based timing for polling operations
   - Use `delay()` sparingly and only for short durations

5. **Feedback Implementation**
   - Query device status after sending commands that change state
   - Implement periodic polling for devices that don't provide unsolicited feedback
   - Parse responses carefully, accounting for protocol-specific formatting

## Future Enhancements

1. **Additional Feedback Signals**
   - Position feedback (pan/tilt coordinates)
   - Zoom position feedback
   - Current preset feedback

2. **Error Handling**
   - Timeout detection for unresponsive cameras
   - Retry mechanism for failed commands
   - Error status reporting

3. **Advanced Features**
   - Auto-discovery of camera address
   - Multiple camera support with address management
   - Extended preset capabilities

## References

1. VISCA Camera Protocol Documentation
2. Crestron SIMPL+ Programming Guide (Doc. 5789D)
3. VISCA Command Documentation
