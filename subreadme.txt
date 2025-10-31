8.31.12.20251029_1 by peter
add:
1.tcp test_arrival
mod:
fix:
1.fromToOnly force K in junction
2.no EndArrival can't move arm
3.no EndArrival can't send 2 in move_cmd(charge station is C)

8.31.12.20251023_1 by HaoChen
add:
mod:
1. uncomment move_cmd direct call in route planning execution workflow algorithm/vehicleRoutePlanner.py
fix:
1. fix threading model for move_cmd execution to use synchronous call instead of daemon thread
- algorithm/vehicleRoutePlanner.py - comment out Thread creation for adapter.move_cmd() to disable daemon thread execution at line 889-891
- algorithm/vehicleRoutePlanner.py - add direct synchronous call to self.adapter.move_cmd(path, go_list) for immediate command execution at line 892
- algorithm/vehicleRoutePlanner.py - maintain is_moving flag setting immediately after move_cmd invocation for proper state tracking
- algorithm/vehicleRoutePlanner.py - preserve is_junction_avoid state transition logic (1->2) after move command execution at line 894-895
- algorithm/vehicleRoutePlanner.py - improve command execution reliability by removing thread-based asynchronous processing overhead
- algorithm/vehicleRoutePlanner.py - enhance route planning determinism by ensuring move commands complete before state transitions

8.31.12.202510201150 by HaoChen
add:
mod:
1.change the data type is_junction_avoid boolean into num algorithm/vehicleRoutePlanner.py
2.update the is_junction_avoid logic same as the tsc 9.4.8



8.31.12.202510030600 by kelvin
add:
mod:
1. add subreadme.txt
fix:

8.31.12.202509191200 by kelvin

mod: 1. modify grating status change event trigger logic to support enhanced lightgate event handling
- erack/KHCPMirleMCSErackAapter.py - replace simple grating_status assignment with comprehensive status change detection at line 3098-3121
- erack/KHCPMirleMCSErackAapter.py - implement intelligent trigger condition evaluation for handle_lightgate_event() calls based on status transitions
- erack/KHCPMirleMCSErackAapter.py - add trigger condition: any status -> "trigger" or "trigger" -> ["closed", "opened"] transitions
- erack/KHCPMirleMCSErackAapter.py - exclude repetitive trigger events: "trigger" -> "trigger", "opened" <-> "closed", and same-state transitions

add: 2. add enhanced logging system for grating status change monitoring and debugging
- erack/KHCPMirleMCSErackAapter.py - add detailed status transition logging with old/new status and trigger decision at line 3107
- erack/KHCPMirleMCSErackAapter.py - implement trigger event confirmation logging for handle_lightgate_event() invocation at line 3114
- erack/KHCPMirleMCSErackAapter.py - add comprehensive status change tracking for improved system monitoring and troubleshooting

mod: 3. modify MCS send flag management to integrate with new status change workflow
- erack/KHCPMirleMCSErackAapter.py - relocate send_grating_status_to_mcs flag updates to status change processing block at line 3118-3121
- erack/KHCPMirleMCSErackAapter.py - maintain existing flag behavior: set True for "trigger" status, False for "closed"/"opened" status
- erack/KHCPMirleMCSErackAapter.py - ensure flag updates occur after status assignment and trigger processing for proper workflow sequence

fix: 4. fix grating status default value handling and null status prevention
- erack/KHCPMirleMCSErackAapter.py - change grating status default value from empty string to "no status" for better error tracking at line 3098
- erack/KHCPMirleMCSErackAapter.py - improve status validation and error detection with meaningful default status value
- erack/KHCPMirleMCSErackAapter.py - enhance debugging capability with clear indication of uninitialized or missing status data

8.31.12.202509181500 by kelvin

add: 1. add INSTALL_CST remote command with enhanced carrier installation control for CST operations
   - semi/e88_mirle_equipment.py - add INSTALL_CST remote command definition with CARRIERID, CARRIERLOC, SHELFSTATE, TEXT parameters at line 1532
   - semi/e88_mirle_equipment.py - implement _on_rcmd_INSTALL_CST() method for enhanced carrier installation workflow with shelf state validation at line 2564-2610
   - semi/e88_mirle_equipment.py - add comprehensive location occupancy checking with existing carrier validation before installation
   - semi/e88_mirle_equipment.py - implement dual-path response handling for successful installations and rejections with detailed logging

mod: 2. modify remote command response codes to use standardized RCMD acknowledgment values
   - semi/e88_mirle_equipment.py - change INSTALL command success response from [0, ack_params] to [4, ack_params] for standardized acknowledgment at line 2629
   - semi/e88_mirle_equipment.py - update REMOVE command success response from [0, ack_params] to [4, ack_params] for consistent response handling at line 2980
   - semi/e88_mirle_equipment.py - modify PICKUPAUTH command success response from [0, ack_params] to [4, ack_params] for unified response protocol at line 3201

fix: 3. fix INSTALL method parameter alignment and signature consistency
   - semi/e88_mirle_equipment.py - correct _on_rcmd_INSTALL method parameter order to match expected function signature at line 2612
   - semi/e88_mirle_equipment.py - ensure consistent parameter passing between INSTALL and INSTALL_CST methods
   - semi/e88_mirle_equipment.py - maintain backward compatibility while fixing parameter alignment issues

8.31.12.202509181500 by kelvin

fix: 1. fix light gate reset event socket communication to prevent connection errors during testing
- simulator/KHCP_GuiErackSimulator.py - comment out clientsock.send() call in reset_light_gate_callback() method to avoid socket send errors at line 1012
- simulator/KHCP_GuiErackSimulator.py - maintain event logging functionality while disabling actual socket transmission for debugging purposes
- simulator/KHCP_GuiErackSimulator.py - preserve light gate reset event data structure and formatting for future socket communication restoration

add: 2. add grating status tracking and event handling integration for enhanced light gate state management
- erack/KHCPMirleMCSErackAapter.py - add grating_status attribute initialization in init method for zone-based status tracking at line 164
- erack/KHCPMirleMCSErackAapter.py - implement GRATING_STATUS payload processing in port data handling workflow at line 3097-3103
- erack/KHCPMirleMCSErackAapter.py - add grating status capture and storage mechanism for real-time light gate state synchronization
- erack/KHCPMirleMCSErackAapter.py - integrate grating status updates with existing carrier data processing pipeline

mod: 3. modify GRATINGCMD processing to optimize status query handling and reduce unnecessary socket operations
- erack/KHCPMirleMCSErackAapter.py - modify handle_gratingcmd() method to only send socket commands for GRATINGREST and GRATINGPASS operations at line 1520-1575
- erack/KHCPMirleMCSErackAapter.py - implement conditional command processing to call handle_lightgate_event() directly for GRATINGSTS queries
- erack/KHCPMirleMCSErackAapter.py - optimize command flow by eliminating redundant socket communication for status-only operations
- erack/KHCPMirleMCSErackAapter.py - enhance processing efficiency by reducing network overhead for status query commands

mod: 4. modify light gate event handling to use internal status tracking instead of external event data
- erack/KHCPMirleMCSErackAapter.py - modify handle_lightgate_event() method signature to remove event_data parameter dependency at line 1865
- erack/KHCPMirleMCSErackAapter.py - implement internal grating_status-based light gate state determination logic at line 1878-1884
- erack/KHCPMirleMCSErackAapter.py - replace external event data dependency with self.grating_status for trigger state evaluation
- erack/KHCPMirleMCSErackAapter.py - update all handle_lightgate_event() method calls to use parameterless invocation at line 2812, 2833

fix: 5. fix GRATINGCMD command name validation and documentation consistency
- semi/e88_mirle_equipment.py - fix GRATINGCMD valid command name from GRATINSTS to GRATINGSTS for proper spelling consistency at line 3864
- erack/KHCPMirleMCSErackAapter.py - fix GRATINGCMD method documentation comment to use correct GRATINGSTS spelling at line 1493
- erack/KHCPMirleMCSErackAapter.py - update valid_commands list to include corrected GRATINGSTS command name at line 1508
- erack/KHCPMirleMCSErackAapter.py - ensure consistent command naming across all GRATINGCMD processing methods

8.31.12.202509180507 by kelvin

add: 1. add comprehensive light gate control system with three-button GUI interface for eRack simulator
- simulator/KHCP_GuiErackSimulator.py - add get_simple_grating_status() method for three-state status detection (opened/trigger/closed) at line 664-684
- simulator/KHCP_GuiErackSimulator.py - implement comprehensive light gate GUI control panel with Manual Trigger, Gate OPEN/CLOSED toggle, and Reset Gate buttons at line 830-863
- simulator/KHCP_GuiErackSimulator.py - add reset_light_gate_callback() method for manual abnormal trigger state recovery at line 930-1025
- simulator/KHCP_GuiErackSimulator.py - add toggle_grating_detection_callback() method for enabling/disabling gate detection with visual feedback at line 908-960

add: 2. add GRATINGREST command integration with automatic reset functionality for zone-based operations
- simulator/KHCP_GuiErackSimulator.py - add execute_light_gate_reset() method for GRATINGREST command processing at line 1026-1094
- simulator/KHCP_GuiErackSimulator.py - implement GRATINGREST command handler integration calling execute_light_gate_reset(zoneid) at line 594-595
- simulator/KHCP_GuiErackSimulator.py - add comprehensive event-driven light gate state management with socket communication for real-time updates
- simulator/KHCP_GuiErackSimulator.py - implement zone-based light gate state tracking with global light_gate_states dictionary support

add: 3. add GRATINGPASS command synchronization with GUI button state updates for bidirectional control
- simulator/KHCP_GuiErackSimulator.py - add update_grating_button_state() method for GRATINGPASS command GUI synchronization at line 1096-1134
- simulator/KHCP_GuiErackSimulator.py - implement GRATINGPASS command handler integration calling update_grating_button_state(zoneid) at line 626-627
- simulator/KHCP_GuiErackSimulator.py - add real-time button appearance updates (Green for OPEN, Red for CLOSED) based on grating_detection_state
- simulator/KHCP_GuiErackSimulator.py - implement zone-specific GUI updates ensuring only current display zone buttons are modified

mod: 4. modify light gate status query system to return actual three-state status instead of empty values
- simulator/KHCP_GuiErackSimulator.py - update query_payload generation to use get_simple_grating_status() method instead of empty GRATING_STATUS at line 1221-1229
- simulator/KHCP_GuiErackSimulator.py - implement enhanced status logging with detailed grating status output for debugging and monitoring
- simulator/KHCP_GuiErackSimulator.py - ensure consistent status reporting across all light gate query operations

fix: 5. fix GRATINGCMD command spelling errors and Auto Mode functionality removal for simplified operation
- simulator/KHCP_GuiErackSimulator.py - remove light_gate_auto_mode global variable and all related automatic trigger simulation code at line 66
- simulator/KHCP_GuiErackSimulator.py - remove auto_trigger_simulation(), toggle_auto_trigger(), and auto_trigger_callback() methods (lines 753-957 removed)
- simulator/KHCP_GuiErackSimulator.py - fix GRATINGPASS and GRATINSTS command spelling in eRackSimulator message processing at line 616 and 631
- semi/e88_mirle_equipment.py - fix GRATINGCMD valid command names from GRAINGPASS/GRAINSTS to GRATINGPASS/GRATINSTS at line 3837, 3864, 3872

fix: 6. fix carrier installation workflow scan request validation and carrier ID mismatch handling
- erack/KHCPMirleMCSErackAapter.py - remove mismatch_carrierID generation and new_carrier_equal_carrierID_before_scanning validation logic in carrier store workflow (lines 959-1004)
- erack/KHCPMirleMCSErackAapter.py - simplify carrier ID assignment to use direct carrier['carrierID'] mapping without mismatch detection at line 975-978
- erack/KHCPMirleMCSErackAapter.py - remove conditional install error sending mechanism and always use id_read with error code 0 at line 993-995
- erack/KHCPMirleMCSErackAapter.py - add id_read() call for scan request processing in same carrier ID scenarios at line 1161

add: 7. add enhanced carrier status button text management for load/unload operations
- simulator/KHCP_GuiErackSimulator.py - add dynamic BUTTON_LOAD_TEXT assignment based on carrier detection status in normal mode operation at line 321, 328, 335
- simulator/KHCP_GuiErackSimulator.py - implement consistent button text setting for load/unload operations (identified carriers show 'unload', empty slots show 'load')
- simulator/KHCP_GuiErackSimulator.py - ensure proper button text synchronization across scan request mode and normal mode carrier handling workflows

8.31.12.202509161200 by kelvin
add:
1. add enhanced carrier position scanning mechanism with mutual lookup functionality
   - semi/e88_mirle_equipment.py - add find_carrier_by_location() method for location-based carrier ID lookup at line 2712-2726
   - semi/e88_mirle_equipment.py - implement find_location_by_carrier() method for carrier ID-based location lookup at line 2728-2743
   - semi/e88_mirle_equipment.py - add comprehensive carrier data validation and error handling with detailed logging
   - semi/e88_mirle_equipment.py - implement enhanced _on_rcmd_SCAN command processing with bidirectional parameter support
2. add comprehensive scan request tracking and validation system for erack operations
   - erack/KHCPMirleMCSErackAapter.py - add scan_request and carrierID_before_scanning fields to lots data structure at line 186
   - erack/KHCPMirleMCSErackAapter.py - implement scan request condition checking in carrier removal workflow for anomaly prevention
   - erack/KHCPMirleMCSErackAapter.py - add scan request flag validation to prevent false positive abnormal removal alarms
   - erack/erack_mgr.py - add enhanced scan command processing with improved parameter validation and error handling
3. add carrier scanning test framework with comprehensive validation scenarios
   - test_scan_command.py - add complete test script for SCAN command mutual lookup functionality validation
   - test_scan_command.py - implement mock carrier data structure for testing bidirectional scanning scenarios
   - test_scan_command.py - add comprehensive test cases for CARRIERID and CARRIERLOC parameter combinations
   - test_scan_command.py - implement simulation framework for _on_rcmd_SCAN method enhancement testing

mod:
1. enhance carrier anomaly detection logic with scan request condition validation
   - erack/KHCPMirleMCSErackAapter.py - modify carrier removal detection to check scan_request flag before triggering abnormal removal alarm
   - erack/KHCPMirleMCSErackAapter.py - improve carrier state validation workflow to prevent false positives during legitimate scanning operations
   - erack/KHCPMirleMCSErackAapter.py - optimize carrier tracking data structure to include pre-scan carrier ID backup for validation
2. optimize erack manager scanning workflow with enhanced parameter processing
   - erack/erack_mgr.py - modify SCAN command handler to support enhanced mutual lookup functionality
   - erack/erack_mgr.py - improve error handling and response validation for scanning operations
   - erack/erack_mgr.py - enhance scanning result processing with better error recovery mechanisms
3. improve GUI simulator logging efficiency and performance optimization
   - simulator/KHCP_GuiErackSimulator.py - modify debug output to reduce console spam during carrier validation
   - simulator/KHCP_GuiErackSimulator.py - optimize carrier ID checking workflow for better performance
   - simulator/KHCP_GuiErackSimulator.py - enhance duplicate detection logic with improved logging control

fix:
1. resolve carrier abnormal removal false positive detection during scanning operations
   - erack/KHCPMirleMCSErackAapter.py - fix abnormal removal alarm triggering by adding scan_request condition check
   - erack/KHCPMirleMCSErackAapter.py - fix carrier state validation to properly handle legitimate scanning workflows
   - erack/KHCPMirleMCSErackAapter.py - fix data structure initialization to include necessary tracking fields for scan operations
2. correct SCAN command parameter handling with bidirectional lookup support
   - semi/e88_mirle_equipment.py - fix _on_rcmd_SCAN method to properly handle both CARRIERID and CARRIERLOC parameters
   - semi/e88_mirle_equipment.py - fix carrier lookup logic to support mutual parameter resolution scenarios
   - semi/e88_mirle_equipment.py - fix error handling and logging consistency for scanning operations
3. resolve test framework compatibility and parameter validation
   - test_scan_command.py - fix test scenario coverage to include all possible parameter combinations
   - test_scan_command.py - fix mock data structure to accurately simulate production carrier management

8.31.12.202509151700 by kelvin
add:
1. add comprehensive scan request recording mechanism for carrier installation error handling
   - erack/KHCPMirleMCSErackAapter.py - add scan_records dictionary attribute initialization in __init__ method at line 157
   - erack/KHCPMirleMCSErackAapter.py - implement scan request recording logic in handle_install_clear method at line 1651-1660
   - erack/KHCPMirleMCSErackAapter.py - add scan request data structure with carrierID, scan flag, timestamp and carrier location tracking
   - erack/KHCPMirleMCSErackAapter.py - add detailed logging for scan request recording events with port index and carrier information
2. add comprehensive scan records management API with query and cleanup functionality
   - erack/KHCPMirleMCSErackAapter.py - add get_scan_records() method for retrieving all or specific port scan records at line 1717-1734
   - erack/KHCPMirleMCSErackAapter.py - implement clear_scan_record() method for removing individual port scan records at line 1736-1756
   - erack/KHCPMirleMCSErackAapter.py - add clear_all_scan_records() method for bulk scan record cleanup at line 1758-1772
   - erack/KHCPMirleMCSErackAapter.py - implement comprehensive error handling and logging for all scan record operations
3. add enhanced status table documentation with detailed color mapping reference
   - erack/KHCPMirleMCSErackAapter.py - add comprehensive Mirle ShelfStatus documentation table with markdown formatting at line 30-41
   - erack/KHCPMirleMCSErackAapter.py - implement detailed color code mapping for all status_no values with hex color codes
   - erack/KHCPMirleMCSErackAapter.py - add usage context descriptions for each status combination and carrier state

mod:
1. enhance carrier installation clear handling with scan request integration
   - erack/KHCPMirleMCSErackAapter.py - modify handle_install_clear method logging to include scan_request parameter at line 1648
   - erack/KHCPMirleMCSErackAapter.py - improve error handling workflow to conditionally record scan requests based on scan_request flag
   - erack/KHCPMirleMCSErackAapter.py - enhance scan request validation and data structure population with timestamp and location information
2. optimize web-based booking workflow with operator-specific status handling
   - erack/KHCPMirleMCSErackAapter.py - modify set_booked_flag method to implement byWeb parameter differentiation for operator vs automated bookings
   - erack/KHCPMirleMCSErackAapter.py - improve operator transfer status assignment with status_no=7 (white) for OP Transfer and status_no=8 (orange) for OP Drop
   - erack/KHCPMirleMCSErackAapter.py - enhance booking workflow with shelf_state mapping (3,4) for manual operations vs (5) for automated transfers
3. improve controller booking command processing with version-based parameter handling
   - controller.py - modify carrier booking command processing to include byWeb=True parameter for KHCP version eracks at line 1040-1043
   - controller.py - enhance version detection logic to maintain backward compatibility with non-KHCP erack versions
   - controller.py - optimize booking workflow differentiation between web-initiated and automated booking operations
4. optimize simulator debug output and logging efficiency
   - simulator/KHCP_GuiErackSimulator.py - modify duplicate carrier ID detection logic to comment out excessive debug printing
   - simulator/KHCP_GuiErackSimulator.py - improve performance by reducing console output during carrier ID validation process
   - tsc.py - modify HoldZone logging from logger.info to print statement for better visibility at line 2011

fix:
1. resolve scan request recording timing and data integrity issues
   - erack/KHCPMirleMCSErackAapter.py - fix scan request recording to only occur when scan_request=True flag is set
   - erack/KHCPMirleMCSErackAapter.py - fix scan record data structure to include complete information (carrierID, scan, timestamp, carrier_loc)
   - erack/KHCPMirleMCSErackAapter.py - fix logging format to properly display scan request recording events with comprehensive context
2. correct web booking parameter handling with proper version detection
   - controller.py - fix set_booked_flag method calls to include byWeb parameter only for KHCP version eracks
   - controller.py - fix booking workflow compatibility to maintain existing functionality for non-KHCP versions
   - erack/KHCPMirleMCSErackAapter.py - fix operator booking status assignment to use correct status_no and background_color combinations
3. resolve status documentation consistency with comprehensive color mapping
   - erack/KHCPMirleMCSErackAapter.py - fix status documentation format from simple text to structured markdown table
   - erack/KHCPMirleMCSErackAapter.py - fix color code references to include complete hex values and descriptive names
   - erack/KHCPMirleMCSErackAapter.py - fix status mapping clarity with explicit usage contexts for each carrier state combination
4. correct simulator output optimization for better performance
   - simulator/KHCP_GuiErackSimulator.py - fix excessive debug output by commenting out repetitive carrier ID validation print statements
   - tsc.py - fix HoldZone monitoring output method to use print instead of logger for immediate visibility

8.31.12.202509141630 by kelvin Date
add:
1. add enhanced web-based booking differentiation mechanism for KHCP erack adapter
   - controller.py - add byWeb parameter support for set_booked_flag() method with KHCP version detection at line 1040-1043
   - controller.py - implement conditional web booking logic based on erack_version configuration
   - erack/KHCPMirleMCSErackAapter.py - add comprehensive status table documentation with detailed color code mapping at line 30-41
   - erack/KHCPMirleMCSErackAapter.py - implement byWeb-based booking workflow differentiation for operator vs automated transfers
2. add operator transfer booking status mechanism with manual operation support  
   - erack/KHCPMirleMCSErackAapter.py - add OP (Operator) transfer status handling for web-based bookings at line 491-503
   - erack/KHCPMirleMCSErackAapter.py - implement status_no=7 for "Busy(Waiting OP Transfer)" with white background color
   - erack/KHCPMirleMCSErackAapter.py - add status_no=8 for "Busy(Waiting OP Drop)" with orange background color for manual operations
   - erack/KHCPMirleMCSErackAapter.py - implement shelf_state=3/4 mapping for operator pickup/drop operations

mod:
1. enhance booking flag method with web-based operation detection
   - controller.py - modify carrier booking command processing to include byWeb=True parameter for KHCP version eracks
   - controller.py - improve booking workflow differentiation between automated and web-initiated booking operations
   - erack/KHCPMirleMCSErackAapter.py - modify set_booked_flag() method to support byWeb parameter with conditional status assignment
2. optimize status documentation with comprehensive color code reference table
   - erack/KHCPMirleMCSErackAapter.py - modify status documentation from simple text format to detailed markdown table format
   - erack/KHCPMirleMCSErackAapter.py - improve status mapping clarity with explicit color codes, names, and usage contexts
   - erack/KHCPMirleMCSErackAapter.py - enhance documentation to include all status_no values (0,1,2,3,6,7,8,9,99) with corresponding colors
3. improve booking workflow with operator-specific status handling
   - erack/KHCPMirleMCSErackAapter.py - modify booking status assignment logic at lines 491-518 to differentiate between web and automated operations
   - erack/KHCPMirleMCSErackAapter.py - enhance status description messages with bilingual OP (Operator) terminology for web bookings
   - erack/KHCPMirleMCSErackAapter.py - improve shelf_state mapping to use proper values (3,4) for manual operations vs (5) for automated transfers

fix:
1. resolve booking status inconsistency between web and automated operations
   - controller.py - fix set_booked_flag() method calls to include proper byWeb parameter for KHCP version differentiation
   - controller.py - fix booking command processing to maintain compatibility with non-KHCP erack versions
   - erack/KHCPMirleMCSErackAapter.py - fix status assignment logic to properly handle operator vs automated booking scenarios
2. correct status color mapping consistency with comprehensive documentation
   - erack/KHCPMirleMCSErackAapter.py - fix status documentation to include complete color code reference for all possible states
   - erack/KHCPMirleMCSErackAapter.py - fix status table formatting to improve readability and maintenance
   - erack/KHCPMirleMCSErackAapter.py - fix background color assignment consistency with documented color codes
3. resolve operator booking workflow with proper status differentiation
   - erack/KHCPMirleMCSErackAapter.py - fix byWeb parameter handling to provide distinct operator transfer status messages
   - erack/KHCPMirleMCSErackAapter.py - fix shelf_state assignments to properly differentiate manual (3,4) vs automated (5) operations
   - erack/KHCPMirleMCSErackAapter.py - fix status_no and background_color assignments for operator pickup (7) and drop (8) scenarios

8.31.12.202509141600 by kelvin Date
add:
1. add comprehensive carrier status validation mechanism for abnormal placement detection
   - erack/KHCPMirleMCSErackAapter.py - add status_no validation logic in carrier store workflow at line 869-895
   - erack/KHCPMirleMCSErackAapter.py - implement carrier placement status check for non-UN carriers during E88_Carriers.Data registration
   - erack/KHCPMirleMCSErackAapter.py - add conditional status assignment based on current status_no values (3,8,5 vs others)
   - erack/KHCPMirleMCSErackAapter.py - add abnormal placement alarm mechanism with status_no=5 for unauthorized carrier installation
2. add enhanced carrier removal status validation with expanded authorized states
   - erack/KHCPMirleMCSErackAapter.py - add status_no=5 to authorized removal states list at line 748
   - erack/KHCPMirleMCSErackAapter.py - implement proper carrier cleanup workflow for normal removal scenarios
   - erack/KHCPMirleMCSErackAapter.py - add create_time reset and installed_time clearing for auto dispatch preparation
3. add carrier installation timing control with status-based conditioning
   - erack/KHCPMirleMCSErackAapter.py - add status_no=0 validation check before carrier installation workflow at line 2918
   - erack/KHCPMirleMCSErackAapter.py - implement conditional carrier registration to prevent duplicate processing
   - erack/test1.py - add time formatting utility functions for timestamp handling and testing

mod:
1. enhance carrier abnormal removal detection with comprehensive status validation
   - erack/KHCPMirleMCSErackAapter.py - modify authorized removal states from [2,7,1,9] to [2,7,1,9,5] for expanded normal removal scenarios
   - erack/KHCPMirleMCSErackAapter.py - improve error status assignment from 'Error' to 'alarm' for better status consistency
   - erack/KHCPMirleMCSErackAapter.py - enhance normal removal workflow with proper carrier metadata cleanup and idle state transition
2. optimize carrier placement workflow with intelligent status-based processing
   - erack/KHCPMirleMCSErackAapter.py - modify carrier store logic at lines 871-895 to implement status-based carrier registration
   - erack/KHCPMirleMCSErackAapter.py - improve status assignment logic with separate handling for authorized (3,8,5) and unauthorized status values
   - erack/KHCPMirleMCSErackAapter.py - enhance carrier installation description with proper English/Chinese bilingual format
3. improve carrier installation timing control with conditional processing
   - erack/KHCPMirleMCSErackAapter.py - modify carrier registration workflow at lines 2918-2932 to include status_no=0 validation
   - erack/KHCPMirleMCSErackAapter.py - enhance duplicate installation prevention by checking existing carrier state before processing
   - erack/KHCPMirleMCSErackAapter.py - improve debug logging with detailed warning messages for installation workflow tracking

fix:
1. resolve carrier placement validation timing issues with proper status checking
   - erack/KHCPMirleMCSErackAapter.py - fix carrier store workflow to validate status_no before E88_Carriers registration
   - erack/KHCPMirleMCSErackAapter.py - fix unauthorized placement detection by implementing status_no range validation (3,8,5)
   - erack/KHCPMirleMCSErackAapter.py - fix alarm status assignment for carriers placed without proper booking authorization
2. correct carrier removal status validation with expanded authorized states
   - erack/KHCPMirleMCSErackAapter.py - fix abnormal removal detection by adding status_no=5 to authorized removal states
   - erack/KHCPMirleMCSErackAapter.py - fix carrier cleanup workflow to properly reset create_time and installed_time for normal removals
   - erack/KHCPMirleMCSErackAapter.py - fix status transition from error state to proper idle state during normal carrier removal
3. resolve duplicate carrier installation processing with status-based prevention
   - erack/KHCPMirleMCSErackAapter.py - fix carrier installation logic to check status_no=0 before processing new carriers
   - erack/KHCPMirleMCSErackAapter.py - fix duplicate carrier registration prevention by implementing proper state validation
   - erack/test1.py - fix time formatting test code to include proper timestamp conversion utilities

8.31.12.202509131530 by kelvin 
add:
1. add carrier abnormal removal detection mechanism with intelligent status validation
   - erack/KHCPMirleMCSErackAapter.py - add comprehensive abnormal removal check logic in carrier update workflow at line 744-766
   - erack/KHCPMirleMCSErackAapter.py - implement current_status_no validation to detect unauthorized carrier removal outside booking states
   - erack/KHCPMirleMCSErackAapter.py - add error state triggering with status_no=5 for abnormal carrier removal incidents
2. add enhanced shelf state change method with flexible remark parameter support
   - erack/KHCPMirleMCSErackAapter.py - add default remark parameter to locstatechg_set() method signature at line 2100
   - erack/KHCPMirleMCSErackAapter.py - implement N2 shelf movement status handling with bilingual description support
   - erack/KHCPMirleMCSErackAapter.py - add RESERVE status handling for cross-equipment booking scenarios

mod:
1. enhance carrier removal workflow with intelligent abnormal detection system
   - erack/KHCPMirleMCSErackAapter.py - modify carrier removal logic at line 743-771 to include pre-removal status validation
   - erack/KHCPMirleMCSErackAapter.py - improve status checking to exclude authorized removal states (2,7,1,9) from abnormal detection
   - erack/KHCPMirleMCSErackAapter.py - enhance normal removal handling with proper Idle status reset and booking flag cleanup
   - erack/KHCPMirleMCSErackAapter.py - add comprehensive logging for abnormal removal incidents with detailed status transition information
2. optimize shelf state management with enhanced remark-based status control
   - erack/KHCPMirleMCSErackAapter.py - modify locstatechg_set() method at lines 2185-2207 to implement conditional status setting based on remark content
   - erack/KHCPMirleMCSErackAapter.py - improve shelf state determination logic with NONE/empty remark handling for standard idle/busy status assignment
   - erack/KHCPMirleMCSErackAapter.py - enhance N2 shelf movement handling with specific status_no=1 and descriptive messaging
   - erack/KHCPMirleMCSErackAapter.py - add RESERVE state processing with status_no=9 for inter-equipment booking coordination
3. improve empty carrier handling consistency across destination setting workflow
   - erack/KHCPMirleMCSErackAapter.py - modify set_destination() method at line 459 to use consistent status_no=0 for empty carrier idle state
   - erack/KHCPMirleMCSErackAapter.py - standardize background color assignment using background_color.get(0,"") for idle state consistency

fix:
1. resolve carrier abnormal removal detection timing issues with pre-modification validation
   - erack/KHCPMirleMCSErackAapter.py - fix abnormal removal check positioning to occur before any status modification in carrier removal workflow
   - erack/KHCPMirleMCSErackAapter.py - fix status validation logic to properly exclude authorized removal states from error triggering
   - erack/KHCPMirleMCSErackAapter.py - fix booking flag cleanup to only occur during normal carrier removal scenarios
2. correct shelf state inconsistency with proper idle status assignment
   - erack/KHCPMirleMCSErackAapter.py - fix status_no assignment consistency from 1 to 0 for empty carrier idle state in set_destination() method
   - erack/KHCPMirleMCSErackAapter.py - fix background color mapping to use proper index 0 for idle state visual representation
3. resolve locstatechg_set method parameter handling with default remark support
   - erack/KHCPMirleMCSErackAapter.py - fix method signature to include default empty string for remark parameter ensuring backward compatibility
   - erack/KHCPMirleMCSErackAapter.py - fix conditional status assignment logic to properly handle NONE and empty remark scenarios
   - erack/KHCPMirleMCSErackAapter.py - fix debug logging integration for status_no tracking in remark-based status assignment workflow

8.31.12.202509131500 by kelvin
add:
1. add intelligent vehicle load request control mechanism with flag-based triggering system
   - vehicles/vehicle.py - add trigerLoadPending flag validation logic to prevent duplicate TrLoadReq events during stocker operations
   - vehicles/vehicle.py - implement conditional should_send logic for load request timing control in parked state workflow
2. add enhanced erack adapter shelf status management with carrier ID validation
   - erack/KHCPMirleMCSErackAapter.py - add port_carrier_ID validation checks in set_destination() method for improved shelf status handling
   - erack/KHCPMirleMCSErackAapter.py - implement bilingual description format with English/Chinese dual language support for shelf status messages
   - erack/KHCPMirleMCSErackAapter.py - add byWeb parameter to set_booked_flag() method for web-based booking differentiation
3. add version control update for release management
   - SubVersion.txt - update version timestamp to 202509111500 for current release tracking

mod:
1. optimize erack adapter shelf status workflow with enhanced carrier ID handling
   - erack/KHCPMirleMCSErackAapter.py - modify set_destination() method at lines 433-461 to include port_carrier_ID variable extraction for consistent 
carrier ID management
   - erack/KHCPMirleMCSErackAapter.py - improve shelf status description format with bilingual "{} Installed,{} 放置" pattern for better user experience
   - erack/KHCPMirleMCSErackAapter.py - enhance empty carrier handling logic to properly set Idle status when no carrier present
   - erack/KHCPMirleMCSErackAapter.py - modify set_booked_flag() method at lines 473-530 to use extracted port_carrier_ID for consistent shelf state 
management
2. enhance vehicle load request state management with selective booking preservation
   - vehicles/vehicle.py - modify TrLoadReq sending logic at lines 1953-1970 to implement trigerLoadPending-based request control mechanism
   - vehicles/vehicle.py - improve load request timing by removing debug logging and simplifying conditional logic flow
   - vehicles/vehicle.py - optimize load request handling at lines 4645-4654 by cleaning up redundant debug statements and improving code readability
3. improve erack adapter booking state preservation logic for critical operations
   - erack/KHCPMirleMCSErackAapter.py - modify booking flag reset logic at lines 2841-2844 to preserve booked status during specific shelf states 
(2,3,7,8,9,1)
   - erack/KHCPMirleMCSErackAapter.py - enhance carrier management workflow to maintain booking integrity during transfer operations

fix:
1. resolve erack adapter shelf status inconsistency with proper carrier ID validation
   - erack/KHCPMirleMCSErackAapter.py - fix shelf status determination in set_destination() to properly handle empty carrier scenarios with appropriate
Idle status assignment
   - erack/KHCPMirleMCSErackAapter.py - fix carrier ID extraction consistency by using port_carrier_ID variable throughout set_booked_flag() method
   - erack/KHCPMirleMCSErackAapter.py - fix locstatechg_update() calls to use extracted port_carrier_ID instead of direct dictionary access
2. correct vehicle load request duplicate sending issue for stocker operations
   - vehicles/vehicle.py - fix TrLoadReq duplicate event generation by implementing trigerLoadPending flag validation logic
   - vehicles/vehicle.py - fix load request timing control by removing unnecessary debug logging and streamlining conditional logic
   - vehicles/vehicle.py - fix code formatting by removing excessive debug statements and improving readability in load request workflow
3. resolve booking state management conflicts during transfer operations
   - erack/KHCPMirleMCSErackAapter.py - fix booking flag preservation logic to prevent unintended status reset during critical shelf states
   - erack/KHCPMirleMCSErackAapter.py - fix carrier management consistency to maintain proper state synchronization during operations

8.31.12.202509111500 by kelvin
add:
1. add comprehensive route rights analysis documentation with detailed technical implementation
   - docs/route_rights_analysis.md - add detailed analysis of static and dynamic route rights mechanisms in TSC system
   - docs/route_rights_analysis.md - implement complete coverage of route planner logic with code examples and best practices
   - docs/route_rights_analysis.md - add technical comparison table between static and dynamic route rights management
   - docs/route_rights_analysis.md - add configuration parameters explanation and performance optimization suggestions
2. add detailed route rights release mechanism documentation with trigger point analysis
   - docs/route_rights_release_analysis.md - add comprehensive analysis of 25+ route rights release trigger points across system
   - docs/route_rights_release_analysis.md - implement detailed explanation of clean_right() and clean_path() methods functionality
   - docs/route_rights_release_analysis.md - add thread safety mechanisms and error handling documentation
   - docs/route_rights_release_analysis.md - add troubleshooting guide and monitoring best practices
3. add optical gate requirements documentation with JSON command examples
   - docs/光閘需求.md - add KHCP erack adapter to GUI simulator communication requirements
   - docs/光閘需求.md - implement detailed JSON command format examples for gate reset, open, close, and status query
   - docs/光閘需求.md - add gratingcmd functionality documentation with device_name interface specifications

mod:
1. enhance JSON document structure consistency in erack adapter communication
   - erack/KHCPMirleMCSErackAapter.py - modify all JSON head structures to use 'device_name' instead of 'device name' for consistency across 8 command methods
   - erack/KHCPMirleMCSErackAapter.py - improve JSON document formatting in manual(), auto(), book(), scan(), gratingcmd(), terminal_display_message(), install_error(), connect(), disconnect() methods
   - erack/KHCPMirleMCSErackAapter.py - enhance gratingcmd method with comprehensive JSON debug logging using formatted output
   - erack/KHCPMirleMCSErackAapter.py - add 'cmd' field to install_error JSON structure for improved command identification
2. optimize vehicle loading request handling with intelligent triggering mechanism
   - vehicles/vehicle.py - modify TrLoadReq sending logic to implement trigerLoadPending flag-based control mechanism
   - vehicles/vehicle.py - enhance load request timing control with last_tr_load_send_time tracking for improved request management
   - vehicles/vehicle.py - improve load request workflow with conditional should_send logic to prevent duplicate requests
   - vehicles/vehicle.py - add comprehensive debugging logging with trigerLoadPending status information for better troubleshooting
3. improve claude.md documentation formatting consistency
   - claude.md - modify version control format example to remove redundant Bash command suffix for cleaner presentation
   - claude.md - enhance documentation structure with consistent timestamp format standards

fix:
1. correct JSON header field naming consistency across erack adapter methods
   - erack/KHCPMirleMCSErackAapter.py - fix 'device name' to 'device_name' standardization in manual(), auto(), book(), scan() methods at lines 1316, 1320, 1324, 1332
   - erack/KHCPMirleMCSErackAapter.py - fix JSON header consistency in gratingcmd(), terminal_display_message(), install_error(), connect(), disconnect() methods at lines 1454, 1528, 1667, 2013, 2016
   - erack/KHCPMirleMCSErackAapter.py - fix gratingcmd duplicate debug logging by implementing proper JSON serialization with ensure_ascii=False parameter
2. resolve vehicle load request timing control issues for stocker operations
   - vehicles/vehicle.py - fix load request sending logic by implementing trigerLoadPending flag mechanism to prevent premature requests
   - vehicles/vehicle.py - fix conditional TrLoadReq triggering with should_send validation logic for improved request timing
   - vehicles/vehicle.py - fix load request state management by properly setting trigerLoadPending=False when entering depositing state
   - vehicles/vehicle.py - fix request timing by adding trigerLoadPending=True when vehicle enters parked state for stocker operations
3. correct documentation formatting standards
   - claude.md - fix version control example format by removing unnecessary Bash command reference for cleaner documentatio

8.31.12.202509090827 by kelvin
add:
1. add enhanced unknown carrier ID prefix support for UN type carriers
   - erack/KHCPMirleMCSErackAapter.py - add 'UN' prefix to unknown carrier type validation in startswith() checking at four locations
   - simulator/KHCP_GuiErackSimulator.py - implement 'UN' prefix support for unknown carrier detection logic in carrier status determination
2. add differentiated carrier ID read error handling mechanism
   - erack/KHCPMirleMCSErackAapter.py - add conditional id_read error type assignment based on carrier prefix (UNKF=1, UNKD=2, others=1)
   - erack/KHCPMirleMCSErackAapter.py - implement error type differentiation for unknown carrier ID processing workflow
3. add robot execution time configuration parameter
   - simulator/tcp_bridge_simulate.py - add -r/--robot-time argument parser for configurable robot execution timing
   - simulator/tcp_bridge_simulate.py - implement robot_time parameter with default 0.1 seconds and assignment to tcp_bridge handler
4. add documentation file for project configuration
   - claude.md - add comprehensive project documentation and configuration guidelines
mod:
1. enhance shelf status documentation and remark handling clarity
   - erack/KHCPMirleMCSErackAapter.py - modify shelf status mapping documentation to clarify remark color codes
   - erack/KHCPMirleMCSErackAapter.py - update status 1 remark from '-' to '1/9' and status 3 remark from '1' to '-'
   - erack/KHCPMirleMCSErackAapter.py - improve status 5 remark from '9' to '-' for better consistency
2. optimize HoldZone queue management logic for stocker operations
   - tsc.py - modify HoldZone queue checking to only apply for stocker-related queues using "stocker" keyword filtering
   - tsc.py - improve queue condition checking logic from 'wq.queueID != HoldZone' to 'wq.queueID == HoldZone'
   - tsc.py - enhance logging to include h_vehicle.AgvState information for better debugging visibility
   - tsc.py - add AgvState validation to exclude 'Unassigned' and 'Removed' states from blocking conditions
3. improve vehicle selection workflow with enhanced HoldZone logging
   - tsc.py - move HoldZone logging inside vehicle iteration loop for better per-vehicle tracking
   - tsc.py - add comprehensive HoldZone information logging for each vehicle candidate evaluation
4. update project documentation with enhanced formatting
   - claude.md - improve documentation structure and readability with proper markdown formatting
fix:
1. correct unknown carrier prefix validation consistency across modules
   - erack/KHCPMirleMCSErackAapter.py - fix four locations of carrier prefix checking to include 'UN' type consistently
   - simulator/KHCP_GuiErackSimulator.py - fix unknown carrier detection to support extended 'UN' prefix validation
2. resolve HoldZone queue blocking logic for proper vehicle assignment
   - tsc.py - fix HoldZone queue checking condition to prevent incorrect vehicle blocking in stocker operations
   - tsc.py - fix queue management to only restrict vehicles when they are in non-terminal states
   - tsc.py - fix iteration logic to check all vehicles before applying HoldZone restrictions
3. correct script formatting and line ending consistency
   - start.sh - fix missing newline at end of file for proper script formatting standards

8.31.12.202509071500 by kelvin
add:
1. add comprehensive UnitAlarmList requirements documentation with connection alarm examples
   - e88_spec/alarm_secsgem_UnitAlarmList_需求.md - add detailed S1F4 response examples for connection-related alarms (30001-30004)
   - e88_spec/alarm_secsgem_UnitAlarmList_需求.md - implement ConnectWarning, SocketNullStringWarning, LinkLostWarning, and SocketFormatWarning alarm format examples
   - e88_spec/alarm_secsgem_UnitAlarmList_需求.md - add comprehensive requirements explanation section with alarm state management rules
   - e88_spec/alarm_secsgem_UnitAlarmList_需求.md - add automatic connection alarm clearing mechanism documentation
2. add enhanced UnitAlarmList filtering logic for active alarms only
   - semi/e88_mirle_equipment.py - add alarm.set status validation to ensure only active alarms appear in S1F4 responses
   - semi/e88_mirle_equipment.py - implement comprehensive alarm state checking with hasattr validation for alarm.set property
mod:
1. enhance UnitAlarmList SECS/GEM response accuracy and consistency
   - semi/e88_mirle_equipment.py - modify SV_UnitAlarmList handling at line 1884-1903 to include all active alarms regardless of type
   - semi/e88_mirle_equipment.py - improve alarm filtering condition from 'not alarm.set' to 'alarm.set' for proper active alarm detection
   - semi/e88_mirle_equipment.py - enhance logging to include alarm set status for better debugging visibility
2. optimize UnitAlarmList requirements documentation with practical examples
   - e88_spec/alarm_secsgem_UnitAlarmList_需求.md - improve S1F4 response format examples with proper MAINSTATE=2 fixed value usage
   - e88_spec/alarm_secsgem_UnitAlarmList_需求.md - enhance alarm categorization explanations for both warning and error alarm types
   - e88_spec/alarm_secsgem_UnitAlarmList_需求.md - expand requirements section to include connection alarm lifecycle management
fix:
1. correct UnitAlarmList alarm filtering logic for proper S1F4 responses
   - semi/e88_mirle_equipment.py - fix alarm inclusion condition to use 'alarm.set=True' instead of 'alarm.set=False' for active alarm detection
   - semi/e88_mirle_equipment.py - fix MAINSTATE value consistency to use fixed value 2 for all alarm types in UnitAlarmList
   - semi/e88_mirle_equipment.py - fix debug logging message accuracy to reflect actual alarm filtering behavior
2. resolve UnitAlarmList requirements documentation inconsistencies
   - e88_spec/alarm_secsgem_UnitAlarmList_需求.md - fix MaintState comment format from 'MaintState#固定2' to '#MaintState固定2' for consistency
   - e88_spec/alarm_secsgem_UnitAlarmList_需求.md - fix requirements description clarity to specify alarm.set=True condition for active alarms
   - e88_spec/alarm_secsgem_UnitAlarmList_需求.md - fix connection alarm clearing behavior documentation to align with implemented automatic clearing mechanism


8.31.12.202509071234 by kelvin
add:
1. add automatic connection alarm clearing mechanism for erack adapter
   - erack/KHCPMirleMCSErackAapter.py - add unit_alarm_clear calls for connection-related warning alarms when connection is restored
   - erack/KHCPMirleMCSErackAapter.py - implement automatic clearing of ConnectWarning (30001) alarm after successful reconnection
   - erack/KHCPMirleMCSErackAapter.py - implement automatic clearing of SocketNullStringWarning (30002) alarm after successful reconnection
   - erack/KHCPMirleMCSErackAapter.py - implement automatic clearing of LinkLostWarning (30003) alarm after successful reconnection
   - erack/KHCPMirleMCSErackAapter.py - implement automatic clearing of SocketFormatWarning (30004) alarm after successful reconnection
   - erack/KHCPMirleMCSErackAapter.py - add comprehensive error handling and logging for alarm clearing operations
2. add enhanced SECS/GEM alarm filtering for active alarms only
   - semi/e88_mirle_equipment.py - add validation logic to ensure only active alarms with valid UnitID are included in S1F4 responses
   - semi/e88_mirle_equipment.py - implement alarm.set status checking to exclude cleared alarms from EnhancedALID responses
mod:
1. enhance connection restoration workflow with automatic alarm management
   - erack/KHCPMirleMCSErackAapter.py - modify connection establishment logic at line 2564-2585 to include connection alarm clearing
   - erack/KHCPMirleMCSErackAapter.py - improve connection recovery process to automatically resolve lingering warning alarms
   - erack/KHCPMirleMCSErackAapter.py - enhance connection state management with proactive alarm cleanup mechanism
2. optimize SECS/GEM alarm response accuracy for S1F4 command
   - semi/e88_mirle_equipment.py - modify SV_AlarmsSetDescription handling at line 1706-1716 to filter active alarms only
   - semi/e88_mirle_equipment.py - improve EnhancedALID response generation to exclude cleared alarms with alarm.set=False
   - semi/e88_mirle_equipment.py - enhance alarm state validation to ensure accurate S1F4 responses
fix:
1. resolve persistent connection alarm display issue after reconnection
   - erack/KHCPMirleMCSErackAapter.py - fix connection alarm clearing logic to automatically clear warning alarms when connection is restored
   - erack/KHCPMirleMCSErackAapter.py - fix alarm lifecycle management to prevent warning alarms from persisting after successful reconnection
   - erack/KHCPMirleMCSErackAapter.py - fix unit_alarm_clear method usage to properly clear UnitAlarmSet type warning alarms
2. correct SECS/GEM alarm response inconsistency in S1F4 handling
   - semi/e88_mirle_equipment.py - fix SV_AlarmsSetDescription to exclude cleared alarms from EnhancedALID response list
   - semi/e88_mirle_equipment.py - fix alarm filtering condition to check both UnitID validity and alarm.set status
   - semi/e88_mirle_equipment.py - fix alarm state synchronization to ensure S1F4 responses reflect actual alarm status


8.31.12.202509070234 by kelvin
add:
1. add non-blocking pickupauth execution mechanism for shelf state management
   - erack/KHCPMirleMCSErackAapter.py - add conditional trigger logic for shelf_state_value=3 in locstatechg_update method
   - erack/KHCPMirleMCSErackAapter.py - implement thread-based non-blocking pickupauth execution to prevent main thread blocking
   - erack/KHCPMirleMCSErackAapter.py - add shelf_id to shelf_no parsing logic using regex pattern r'P(\d+)' for proper parameter conversion
   - erack/KHCPMirleMCSErackAapter.py - add comprehensive error handling and logging for pickupauth thread execution
2. add enhanced carrier ID validation logic for simulator
   - simulator/KHCP_GuiErackSimulator.py - add prefix-based carrier ID validation for UNKF, UNKD, UNKS, UNKE types
   - simulator/KHCP_GuiErackSimulator.py - implement conditional carrier status determination based on carrier ID prefix validation
mod:
1. enhance LOCSTATECHG command processing with automated pickup authorization
   - erack/KHCPMirleMCSErackAapter.py - modify locstatechg_update method at line 529-609 to include pickupauth integration
   - erack/KHCPMirleMCSErackAapter.py - improve shelf state management workflow to automatically trigger pickup authorization for manual pickup state
   - erack/KHCPMirleMCSErackAapter.py - enhance thread safety with daemon thread implementation for background pickupauth execution
2. optimize carrier ID handling in E88 communication protocol  
   - semi/e88_mirle_equipment.py - modify install_clear command to use direct CARRIERID instead of FailureIDGEN transformation
   - semi/e88_mirle_equipment.py - update S2F42 response handling to return proper acknowledgment with carrier ID parameter
3. improve simulator carrier detection logic for better identification accuracy
   - simulator/KHCP_GuiErackSimulator.py - enhance unknown carrier detection logic with prefix validation mechanism
   - simulator/KHCP_GuiErackSimulator.py - optimize carrier status assignment based on carrier ID format validation
fix:
1. correct non-blocking execution for shelf state change operations
   - erack/KHCPMirleMCSErackAapter.py - fix locstatechg_update method to execute pickupauth asynchronously when shelf_state_value=3
   - erack/KHCPMirleMCSErackAapter.py - fix thread management by implementing daemon thread with proper exception handling
   - erack/KHCPMirleMCSErackAapter.py - fix shelf_id parsing logic to extract correct shelf_no from shelf identifier format
2. resolve E88 carrier ID communication inconsistencies
   - semi/e88_mirle_equipment.py - fix install_clear remote command to preserve original carrier ID without transformation
   - semi/e88_mirle_equipment.py - fix S2F42 acknowledgment response to include proper CARRIERID parameter instead of generic ack_params
3. correct simulator carrier status determination logic
   - simulator/KHCP_GuiErackSimulator.py - fix carrier detection logic to properly handle non-unknown carrier types
   - simulator/KHCP_GuiErackSimulator.py - fix status assignment to show EMPTY state when carrier ID doesn't match unknown prefixes

8.31.12.202509051837 by kelvin
add:
1. add conditional FAILUREID generation mechanism for unknown carrier types
   - erack/KHCPMirleMCSErackAapter.py - add carrier ID prefix validation logic to determine FAILUREID generation method
   - erack/KHCPMirleMCSErackAapter.py - implement startswith() checking for UNKF, UNKD, UNKS, UNKE prefixes
   - erack/KHCPMirleMCSErackAapter.py - add support for UNKS and UNKE unknown carrier types with direct ID assignment
2. add enhanced alarm information categorization system
   - erack/KHCPMirleMCSErackAapter.py - add alarm_info variable for differentiated error message handling
   - erack/KHCPMirleMCSErackAapter.py - implement UNKF and UNKD specific alarm descriptions for better troubleshooting
   - erack/KHCPMirleMCSErackAapter.py - add error message categorization for slot status display
3. add improved duplicate carrier detection debugging
   - simulator/KHCP_GuiErackSimulator.py - add detailed debug logging for carrier ID validation process
   - simulator/KHCP_GuiErackSimulator.py - implement comprehensive carrier comparison logging with slot index tracking
mod:
1. enhance FAILUREID generation with intelligent prefix-based routing
   - erack/KHCPMirleMCSErackAapter.py - modify four FAILUREID generation locations to use conditional logic based on carrier prefix
   - erack/KHCPMirleMCSErackAapter.py - implement direct carrier ID assignment for UNKF/UNKD/UNKS/UNKE prefixes instead of E88.FailureIDGEN
   - erack/KHCPMirleMCSErackAapter.py - maintain backward compatibility with E88.FailureIDGEN for standard carrier IDs
2. optimize alarm status description generation
   - erack/KHCPMirleMCSErackAapter.py - replace hardcoded alarm messages with dynamic alarm_info variable assignment
   - erack/KHCPMirleMCSErackAapter.py - improve slot error display to show specific error types based on carrier ID prefix
   - erack/KHCPMirleMCSErackAapter.py - enhance error handling with contextual alarm descriptions
3. improve duplicate carrier detection reliability
   - simulator/KHCP_GuiErackSimulator.py - optimize duplicate detection loop with proper break statement execution
   - simulator/KHCP_GuiErackSimulator.py - enhance debugging output for carrier validation process visibility
fix:
1. correct FAILUREID assignment logic for unknown carrier types
   - erack/KHCPMirleMCSErackAapter.py - fix FAILUREID generation at line 644-647 to use conditional prefix checking instead of direct assignment
   - erack/KHCPMirleMCSErackAapter.py - fix FAILUREID generation at line 857-860 for duplicate carrier handling scenarios
   - erack/KHCPMirleMCSErackAapter.py - fix FAILUREID generation at line 890-893 for carrier mapping conflicts
   - erack/KHCPMirleMCSErackAapter.py - fix FAILUREID generation at line 923-926 for existing carrier mapping duplicate cases
2. resolve alarm message consistency and accuracy
   - erack/KHCPMirleMCSErackAapter.py - fix alarm status message to use dynamic alarm_info instead of hardcoded string
   - erack/KHCPMirleMCSErackAapter.py - fix slot description assignment to show appropriate error messages for UNKF and UNKD carriers
   - erack/KHCPMirleMCSErackAapter.py - fix error message localization with proper Chinese descriptions for different carrier types
3. correct duplicate carrier detection break logic
   - simulator/KHCP_GuiErackSimulator.py - fix duplicate detection loop break statement placement to ensure complete validation
   - simulator/KHCP_GuiErackSimulator.py - fix carrier comparison logic to properly identify duplicate scenarios

8.31.12.20250905050722 by kelvin
add:
1. add duplicate carrier ID detection mechanism for simulator
   - simulator/KHCP_GuiErackSimulator.py - add duplicate carrier ID validation logic to prevent multiple slots showing same carrier
   - simulator/KHCP_GuiErackSimulator.py - implement UNKD prefix generation for duplicate carrier scenarios with timestamp suffix
   - simulator/KHCP_GuiErackSimulator.py - add debug logging to track slot_datasets length and duplicate detection process
mod:
1. enhance carrier state management in erack transfer process
   - erack/KHCPMirleMCSErackAapter.py - modify FAILUREID carrier state transition from wait_out() to store() for proper state handling
   - erack/KHCPMirleMCSErackAapter.py - improve carrier transfer completion flow by using store state instead of wait_out state
2. optimize unknown carrier ID format consistency
   - simulator/KHCP_GuiErackSimulator.py - standardize unknown carrier ID format by removing underscore separators for cleaner identification
   - simulator/KHCP_GuiErackSimulator.py - unify carrier ID generation pattern to use compact format without delimiter between components
fix:
1. correct carrier state transition for transfer completion
   - erack/KHCPMirleMCSErackAapter.py - fix FAILUREID carrier state to properly transition to store() instead of wait_out() after transfer operation
   - erack/KHCPMirleMCSErackAapter.py - fix carrier transfer workflow to ensure correct final state for proper E88 communication
2. resolve duplicate carrier ID display issues in simulator
   - simulator/KHCP_GuiErackSimulator.py - fix duplicate carrier ID detection loop with proper break statement placement
   - simulator/KHCP_GuiErackSimulator.py - fix carrier status assignment to show ERROR state when duplicate detected
   - simulator/KHCP_GuiErackSimulator.py - fix unknown carrier ID generation to use consistent naming convention without extra underscores

8.31.12.20250905122759 by kelvin
add:
1. add E88 HOST variable query documentation
   - e88_spec/變數查詢.md - document S1F3 command with parameter 51 for carrier information query
   - e88_spec/變數查詢.md - document S1F3 command with parameter 107 for stocker unit status query
   - e88_spec/變數查詢.md - provide HOST communication examples with secsgem response data structure
   - e88_spec/變數查詢.md - include carrier mapping and slot status information for ERACK21 troubleshooting
2. add dynamic port name mapping system for flexible row-col configuration
   - simulator/KHCP_GuiErackSimulator.py - add row and col instance variables to eRackSimulator class initialization
   - simulator/KHCP_GuiErackSimulator.py - implement dynamic port_name dictionary generation based on row and col parameters
   - simulator/KHCP_GuiErackSimulator.py - add mathematical formula for row-column position calculation using i//col+1 and i%col+1
3. add random module import for enhanced carrier ID generation
   - simulator/KHCP_GuiErackSimulator.py - add random module import to support diverse ID generation methods
mod:
1. enhance unknown carrier ID generation with structured naming convention
   - simulator/KHCP_GuiErackSimulator.py - improve get_id method to generate structured carrier IDs with format UNKF_{name}{port}_{timestamp}
   - simulator/KHCP_GuiErackSimulator.py - integrate name1 parameter and port_name mapping into carrier ID generation for better identification
   - simulator/KHCP_GuiErackSimulator.py - replace simple timestamp format with comprehensive naming scheme including device and location information
2. optimize port name mapping architecture for scalability
   - simulator/KHCP_GuiErackSimulator.py - replace static port_name dictionary with dynamic generation algorithm
   - simulator/KHCP_GuiErackSimulator.py - implement row-column based mapping logic to support arbitrary grid configurations
   - simulator/KHCP_GuiErackSimulator.py - enhance __init__ method to accept global row and col parameters for flexible initialization
3. improve carrier error handling and logging accuracy
   - erack/KHCPMirleMCSErackAapter.py - enhance logging to show CarrierLoc instead of port_no for better debugging visibility
   - erack/KHCPMirleMCSErackAapter.py - add debug logging for E88_Carriers.Mapping to track carrier location mapping
   - erack/KHCPMirleMCSErackAapter.py - improve TURNBACK status handling by using carrierID from port data instead of errorCode
fix:
1. correct port name mapping duplicate issue and inconsistencies
   - simulator/KHCP_GuiErackSimulator.py - fix duplicate port_name mapping where index 1 and 4 both pointed to "-1-2"
   - simulator/KHCP_GuiErackSimulator.py - fix index 4 mapping from incorrect "-1-2" to correct "-2-1" position
   - simulator/KHCP_GuiErackSimulator.py - fix hardcoded static dictionary limitations by implementing dynamic calculation
2. correct carrier ID assignment for RFID read error handling
   - erack/KHCPMirleMCSErackAapter.py - fix FAILUREID generation to use direct carrier carrierID instead of E88.FailureIDGEN function
   - erack/KHCPMirleMCSErackAapter.py - fix TURNBACK status processing to properly assign carrierID from port data
   - simulator/KHCP_GuiErackSimulator.py - fix empty carrier ID handling to generate meaningful structured identifiers instead of incomplete format strings


8.31.12.20250904055806 by kelvin
add:
mod:
1. enhance VehicleRouteFailed event reporting with interval control mechanism
   - algorithm/vehicleRoutePlanner.py - add interval control variables to RoutePlanner class initialization for VehicleRouteFailed event timing management
   - algorithm/vehicleRoutePlanner.py - implement last_route_failed_time tracking variable to record timestamp of last VehicleRouteFailed event trigger
   - algorithm/vehicleRoutePlanner.py - add route_failed_interval configuration variable set to 5 seconds for controlling event frequency
   - algorithm/vehicleRoutePlanner.py - modify VehicleRouteFailed event trigger logic to include time interval checking before sending event
   - algorithm/vehicleRoutePlanner.py - implement current_time comparison with last_route_failed_time to prevent frequent event triggering during blocking situations
   - algorithm/vehicleRoutePlanner.py - update event sending mechanism to record current_time as last_route_failed_time after successful event transmission
   - algorithm/vehicleRoutePlanner.py - enhance E82.report_event call with conditional execution based on 5-second interval threshold
fix:
1. correct VehicleRouteFailed event frequency to prevent system overload
   - algorithm/vehicleRoutePlanner.py - fix VehicleRouteFailed event triggering from every second to 5-second intervals during route blocking scenarios
   - algorithm/vehicleRoutePlanner.py - fix event flooding issue that occurred when vehicles encountered persistent route blocking conditions
   - algorithm/vehicleRoutePlanner.py - fix excessive E82 event reporting that could impact system performance during traffic congestion
   - algorithm/vehicleRoutePlanner.py - fix interval control implementation to maintain original functionality while reducing event frequency
   - algorithm/vehicleRoutePlanner.py - fix time-based throttling mechanism to ensure events are still reported but at controlled intervals

8.31.12.20250903074325 by kelvin
add:
1. add shelf state synchronization mechanism for booked status changes
   - erack/KHCPMirleMCSErackAapter.py - add locstatechg_update calls to synchronize shelf state with E88 system during booking operations
   - erack/KHCPMirleMCSErackAapter.py - implement shelf state tracking variables (shelf_state=5 for booked, shelf_state=1 for available) in set_booked_flag method
mod:
1. enhance erack booking status management with differentiated status codes
   - erack/KHCPMirleMCSErackAapter.py - improve set_booked_flag method to use distinct status codes based on carrier presence (status_no=2 for Pick, status_no=3 for Drop)
   - erack/KHCPMirleMCSErackAapter.py - optimize booking logic to properly differentiate between pickup and drop operations with appropriate status assignments
   - erack/KHCPMirleMCSErackAapter.py - enhance carrier-aware status determination for more accurate slot display during booking operations
   - erack/KHCPMirleMCSErackAapter.py - integrate shelf state management with booking status changes for consistent E88 communication
fix:
1. correct booking status code assignment and shelf state synchronization
   - erack/KHCPMirleMCSErackAapter.py - fix status_no assignment logic to use 2 (Waiting Pick) when carrier exists and 3 (Waiting Drop) when empty slot
   - erack/KHCPMirleMCSErackAapter.py - fix missing shelf state updates during booking status changes to ensure proper E88 system synchronization
   - erack/KHCPMirleMCSErackAapter.py - fix background color consistency by maintaining proper color mapping for booking states


8.31.12.20250903070557 by kelvin
add:
mod:
1. enhance erack slot status management with intelligent carrier detection
   - erack/KHCPMirleMCSErackAapter.py - improve set_booked_flag method to intelligently determine slot status based on carrierID presence
   - erack/KHCPMirleMCSErackAapter.py - optimize booking status logic with carrier-aware status determination for accurate slot display
   - erack/KHCPMirleMCSErackAapter.py - refactor status assignment to show 'Busy' when carrier present and 'Idle' when empty after booking clear
   - erack/KHCPMirleMCSErackAapter.py - improve logging accuracy by using actual carrier data instead of parameter values
   - erack/KHCPMirleMCSErackAapter.py - enhance transfer direction handling with proper status name assignment based on carrier availability
   - erack/KHCPMirleMCSErackAapter.py - consolidate status management logic with comprehensive carrier detection for both booked and unbooked states
fix:
1. correct slot status display inconsistencies and carrier information handling
   - erack/KHCPMirleMCSErackAapter.py - fix slot status_no assignment to use 99 (Busy) when carrier exists instead of always 0 (Idle)
   - erack/KHCPMirleMCSErackAapter.py - fix desc message assignment to show correct carrier installation status when carrier present
   - erack/KHCPMirleMCSErackAapter.py - fix status_name assignment logic to properly reflect carrier presence in slot display
   - erack/KHCPMirleMCSErackAapter.py - fix background color mapping to use appropriate colors (99=gray for Busy, 0=green for Idle) based on carrier status

8.31.12.20250903063735 by kelvin
add:
1. add E88 erack display troubleshooting documentation
   - e88_spec/erack顯示的問題.md - document erack display issue analysis for alarm status desc field disappearing problem in ERACK21 slots
mod:
1. enhance project documentation structure and content organization
   - claude.md - restructure documentation with table of contents and comprehensive sections
   - claude.md - add detailed E88 communication mechanism documentation including HOST->secsgem->e88_mirle_equipment.py->KHCPMirleMCSErackAapter.py pipeline
   - claude.md - add comprehensive alarm processing mechanism documentation with Warning vs Error alarm categorization
   - claude.md - document alarm code 20002 special handling process and dual processing mechanism
   - claude.md - reorganize development guidelines and PR file writing requirements into structured sections
2. improve erack alarm handling and slot status management
   - erack/KHCPMirleMCSErackAapter.py - add get_alarm_unitid method for flexible UnitID configuration based on alarm codes
   - erack/KHCPMirleMCSErackAapter.py - implement alarm_unitid_config mapping for port-level vs zone-level alarm identification
   - erack/KHCPMirleMCSErackAapter.py - enhance slot status preservation during alarm states to prevent desc field clearing
   - erack/KHCPMirleMCSErackAapter.py - update alarm 20002 UnitID to use CarrierLoc for port-level identification instead of device_id
   - erack/KHCPMirleMCSErackAapter.py - improve alarm status handling to maintain alarm descriptions during status transitions
   - erack/KHCPMirleMCSErackAapter.py - optimize error handling to preserve alarm information when clearing non-alarm statuses
fix:
1. correct alarm message terminology and logging
   - erack/KHCPMirleMCSErackAapter.py - update alarm 20002 description from 'Carrier check by operator' to 'rfid read error,rfid 讀取失敗' for accuracy
   - erack/KHCPMirleMCSErackAapter.py - simplify alarm status logging to remove redundant message parameter
   - erack/KHCPMirleMCSErackAapter.py - fix slot status restoration to show 'Busy' instead of 'Idle' when carrier is installed after alarm clear


8.31.12.20250903042300 by kelvin
add:
mod:
1. enhance project documentation with E88 Mirle Equipment integration details  
   - claude.md - add comprehensive E88 Mirle equipment processing flow documentation
   - include HOST communication pipeline: HOST -> secsgem -> e88_mirle_equipment.py -> KHCPMirleMCSErackAapter.py -> KHCP_GuiErackSimulator.py  
   - specify secsgem package installation path and e88_spec requirements storage location
fix:

8.31.12.20250902103905 by kelvin
add:
mod:
1. update Autostart.py import statement
   - Autostart.py - add test comment to subprocess import line for development testing
2. enhance SubVersion.txt timestamp
   - SubVersion.txt - update timestamp to 20250902103213 for precise version tracking
3. add PR documentation to project guide
   - claude.md - add comprehensive PR file writing requirements and guidelines for /write_comint command
fix:

8.31.12.20250902100000 by kelvin
add:
1. add E88 equipment specification documentation
   - e88_spec/E-Rack 儲位狀態顯示對照表.md - E-Rack shelf status display mapping table
   - e88_spec/LightGateState 上報事件和 GRATINGCMD 相關需求.md - LightGate status reporting and GRATINGCMD requirements
   - e88_spec/PICKUPAUTH.md - pickup authorization specifications
   - e88_spec/shelfID與idx說明.md - shelf ID and index documentation
   - e88_spec/新ISNTALL_CMD需求.md - new INSTALL_CMD requirements
mod:
1. enhance KHCP Mirle MCS erack adapter functionality
   - erack/KHCPMirleMCSErackAapter.py - add Mirle shelf status color mapping documentation and background color definitions
   - implement comprehensive shelf status mapping (AVAIL, PROHIBIT, PICKUP Manual, RESERVED Manual, RESERVED AUTO)
   - add predefined background color mapping for different shelf status indicators
2. update rack port format parsing for K25 compatibility  
   - global_variables.py - modify RackPortFormat[18] pattern from ER format to ERACK format for K25 system
   - tools.py - extend rack naming compatibility to support format 18 and 62 in TI Baguio style parsing
3. fix erack data field naming consistency
   - protocol/erack_data_KHCP.py - correct field name from 'backgroup_color' to 'background_color'
fix:

8.31.12.20250829 by kelvin 
add:
1. add start.sh startup script configuration
   - start.sh - shell script for starting TSC system with different configurations
   - FT mode: basic controller startup with API v3_K25
   - FT + CP mode: enhanced startup with KHCP Mirle MCS integration (-e88_eq mirle -erack KHCP)
2. add Python package installation documentation
   - docs/如何安裝缺失的python套件.md - documentation for installing missing Python packages
mod:
1. update .gitignore configuration
   - remove start.sh from gitignore to track startup script in repository
   - maintain exclusion of log files, param files, and dijkstra_map.txt/pose_table.txt
fix:
8.31.12.20250829 by kelvin 
add:
1. add KHCP Mirle MCS support for E88 equipment integration
   - erack/KHCPMirleMCSErackAapter.py - new erack adapter for KHCP Mirle MCS system
   - protocol/erack_data_KHCP.py - KHCP protocol data definitions and formatting
   - semi/e88_mirle_equipment.py - Mirle-specific E88 equipment implementation
   - simulator/KHCP_GuiErackSimulator.py - GUI simulator for KHCP erack testing
2. add enhanced E88 equipment variant selection
   - new -e88_eq command line argument for choosing equipment variant (standard/mirle)
   - dynamic equipment class selection based on configuration
3. add start.sh configuration reference documentation
   - docs/start.sh.設定參考.md - startup parameter examples for different deployment scenarios
   - configuration examples for FT-only and FT+CP/CP modes
4. add readme.txt
mod:
1. enhance controller.py for KHCP integration
   - import semi.e88_mirle_equipment as E88Mirle for KHCP support
   - add -e88_eq parameter parsing and global variable assignment
   - implement dynamic equipment class selection (E88Equipment vs E88MirleEquipment)
2. major refactoring of erack/erack_mgr.py
   - extensive restructuring with 1250+ line changes for improved KHCP compatibility
   - enhanced erack management functionality
3. update global_variables.py for KHCP protocol support
   - import protocol.erack_data_KHCP as erack_khcp
   - add 'KHCP':erack_khcp mapping to global_erack_item dictionary
4. enhance SECS/GEM data items and host management
   - semi/E88_dataitems.py - extensive updates (2015+ line changes) for improved data handling
   - semi/SecsHostMgr.py - enhanced host management with equipment class support
5. update core system components for KHCP compatibility
   - tools.py - enhanced utility functions
   - tr_wq_lib.py - improved transfer waiting queue library
   - tsc.py - core TSC system updates
   - vehicles/transporter.py - transporter functionality enhancements
   - vehicles/vehicle.py - vehicle management improvements
   - web_service_log.py - enhanced logging capabilities
fix:

8.31.12.20250828 by kelvin
add:
1. add CPU monitoring and process analysis functionality in controller.py
   - real-time CPU and RAM usage monitoring with per-core statistics
   - high CPU usage process detection and analysis (>90% threshold)
   - thread-level analysis for controller.py processes
   - automatic monitoring thread with configurable intervals
   - dedicated CPU logger with file rotation
2. add new psutil wheels for system monitoring support
   - Resources/psutil-6.1.1-cp27-cp27mu-manylinux2010_x86_64.whl for Python 2.7
   - Resources/psutil-6.1.1-cp36-abi3-manylinux_2_12_x86_64.manylinux2010_x86_64.manylinux_2_17_x86_64.manylinux2014_x86_64.whl for Python 3.6+
3. add raw message logging functionality in vehicleAdapter.py
   - bidirectional message logging (TSC => MR and TSC <= MR)
   - enhanced debugging capability for vehicle communication
   - dedicated raw logger with automatic log rotation
   - message size and system ID tracking
4. add comprehensive technical documentation for vehicleRoutePlanner FromToOnly mode
   - docs/vehicleRoutePlanner_FromToOnly模式技術說明.md
   - detailed implementation analysis of FromToOnly path processing logic in algorithm/vehicleRoutePlanner.py
   - system architecture overview from UI_Mgmt to vehicleRoutePlanner
   - code analysis of junction node processing, special node handling, and path reconstruction
   - practical usage examples and debugging guidelines
mod:
1. enhance system monitoring capabilities
   - import psutil in controller.py for system resource monitoring
   - add threading support for background CPU monitoring
   - integrate CPU monitoring thread with daemon mode
2. enhance vehicle communication debugging
   - add raw message logger initialization in vehicleAdapter constructor
   - enhance message processing with detailed logging
   - add raw communication logs for both send and receive operations
3. enhance claude.md with structured project documentation
   - reorganized TSC_8.31.12_FT project overview with clear architecture description
   - improved code standards and review guidelines formatting
   - added comprehensive system architecture layers (control, communication, device management)
   - updated technical stack information and system features
fix:

8.31.12.20250812 by peter
add:
1. add guidance for fromToOnly
mod:
fix:
1.fix hitchhiking schedule bug

8.31.12.20250801 by kelvin Date
add:
1. in Resource add tsc_KHFT.service (this serveice only use in vm can not use in server)
2. add how to copy server file to systemd.md
mod:
fix:
