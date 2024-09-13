# HDL-ELEVATOR
ELEVATOR_CONTROL

`Aim:`
To stimulate and synthesis ELEVATOR_CONTROL using Vivado

`Software Required:`
vivado 2023.2 software.

`Procedure:`

STEP:1 Start the vivado software, Select and Name the New project.<br>
STEP:2 Select the device family, device, package and speed.<br>
STEP:3 Select new source in the New Project and select Verilog Module as the Source type.<br>
STEP:4 Type the File Name and module name and Click Next and then finish button. Type the code and save it.<br>
STEP:5 Select the run simulation and then run Behavioral Simulation in the Source Window and click the check syntax.<br>
STEP:6 Click the simulation to simulate the program and give the inputs and verify the outputs as per the truth table.<br>
STEP:7 compare the output with truth table. <br>

`STATE DIAGRAM`

![image](https://github.com/user-attachments/assets/56dbbb56-96fc-4e24-bc7d-d415f28bff00)


`Program`

module Lift8(clk, reset, req_floor, idle, door, Up, Down, current_floor, requests, max_request, min_request, emergency_stop);<br>
input clk, reset, emergency_stop;<br>
input logic [2:0] req_floor; // 3-bit input for 8 floors (0 to 7) <br>
output logic [1:0] door;<br>
output logic [2:0] max_request;<br>
output logic [2:0] min_request;<br>
output logic [1:0] Up;<br>
output logic [1:0] Down;<br>
output logic [1:0] idle;<br>
output logic [2:0] current_floor;<br>
output logic [7:0] requests;<br>
logic door_timer;<br>
logic emergency_stopped;<br>
logic flag=0;<br>
// Update requests when a new floor is requested<br>
always @(req_floor)<br>
begin<br>
requests[req_floor] = 1; // Update max_request and min_request based on requested floors<br>
if (max_request < req_floor) <br>
begin<br>
max_request = req_floor; <br>
end <br>
if (min_request > req_floor) <br>
begin <br>
min_request = req_floor; <br>
end <br>
// Update max_request and min_request based on current floor <br>
if (requests[max_request] == 0 && req_floor > current_floor) <br>
begin <br>
max_request = req_floor; <br>
end <br>
if (requests[min_request] == 0 && req_floor < current_floor) <br>
begin <br>
min_request = req_floor; <br>
end end <br>
// Check and update lift behavior based on current floor <br>
always @(current_floor ) <br>
begin <br>
if (requests[current_floor] == 1) <br>
begin <br>
idle = 1; <br>
door = 1; <br>
requests[current_floor] = 0; <br>
door_timer = 1; // Start the door timer when opening <br>
end end <br>
// State machine for lift control <br>
always @(posedge clk ) <br>
begin <br>
if (door_timer == 1) <br>
begin <br>
door <= 0; // Close the door after the one clock expires <br>
//$display("%h", current_floor); <br>
end <br>
if (reset) <br>
begin <br>
// Reset lift to initial state <br>
flag=0; <br>
current_floor <= 0; <br>
idle <= 0; <br>
door <= 0; // door open <br>
Up <= 1; // going up <br>
Down <= 0; // not going down <br>
max_request <= 0; <br>
min_request <= 7; <br>
requests <= 0; <br>
emergency_stopped <= 0; // Initialize emergency stop state <br>
end <br>
else if (requests == 0 && !reset) <br>
begin <br>
// Stay on the current floor if no requests <br>
current_floor <= current_floor; <br>
emergency_stopped <= 0; // Clear emergency stop when not moving <br>
end <br>
// emergency <br>
else if (emergency_stop) <br>
begin <br>
// Emergency stop button is turned on <br>
idle <= 1; <br>
flag <=1; <br>
emergency_stopped <= 1; // Set emergency stop state <br>
end <br>
else if (emergency_stopped && emergency_stop) <br>
begin <br>
// Remain stopped until the emergency stop button is reset <br>
current_floor <= current_floor; <br>
door <= 0; // Keep the door closed during an emergency stop <br>
end <br>
// emergency reset <br>
else if (!emergency_stop && flag) <br>
begin <br>
// Emergency stop button is turned off <br>
emergency_stopped <= 0; // Set emergency stop state <br>
flag <=0; <br>
end <br>
else <br>
begin <br>
// Normal operation when not in emergency stop <br>
if (max_request <= 7) <br>
begin <br>
if (min_request < current_floor && Down == 1) <br>
begin <br>
   // Move down one floor  <br>
   current_floor <= current_floor - 1;  <br>
   door <= 0;  <br>
   idle <= 0;  <br>
  end  <br>
else if (max_request > current_floor && Up == 1)  <br>
begin  <br>
 // Move up one floor  <br>
   current_floor <= current_floor + 1;  <br>
   door <= 0;  <br>
   idle <= 0;  <br>
  end <br>

else if (req_floor == current_floor)  <br>
begin  <br>
   // Open door and handle request  <br>
     door <= 1;  <br>
     idle <= 1;  <br>
  end  <br>
else if (max_request == current_floor)  <br>
begin  <br>
 Up <= 0;  <br>
Down <= 1;  <br>
end  <br>
else if (min_request == current_floor)  <br>
begin  <br>
Up <= 1;  <br>
  Down <= 0;  <br>
  end  <br>
end  <br>

end end <br>
endmodule <br>

`Output`

![image](https://github.com/user-attachments/assets/a6ec36a1-36d0-4c88-8d63-7ed86bbb7a04)

`Result`
Thus the verilog program for ELEVATOR_CONTROL has been simulated and verified successfully.
