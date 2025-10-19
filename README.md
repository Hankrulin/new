#### Assignment 3 — Paxos Council Election

A nine node Paxos implementation for electing a single council president (M1–M9). Each node runs all three roles (Proposer / Acceptor / Learner) and communicates over TCP. Runtime profiles simulate delay, loss, and crashes to validate fault tolerance and liveness.

<img width="949" height="552" alt="截圖 2025-10-20 凌晨2 44 32" src="https://github.com/user-attachments/assets/8522e8dd-dea6-4d2e-8a9d-3cd29ee65e12" />

### Key classes
CouncilMember – main entry; wires roles + net; starts business & control servers.
Proposer / Acceptor / Learner – Paxos roles.
ServerLoop / Transport – TCP server/client (one JSON line per connection).
Message / MessageType – message schema + (minimal) JSON ser/de; Message.compact() for uniform logs.
NetworkConfig – loads network.config, exposes member → host:port map.
Profiles – injects delay/loss/crash on send/receive.
TriggerClient – Java helper to send control commands (no dependency on nc).

### Build & Run:
1.build the java
cd Assignment3/system
javac java/*.java
2. Create the place to save the log and open the port for each member listening
mkdir -p logs_9
java -cp ./java CouncilMember M1 --config ../network.config --profile reliable --port 9001 --control-port 10001 > logs_9/M1.log 2>&1 &
java -cp ./java CouncilMember M2 --config ../network.config --profile reliable --port 9002 --control-port 10002 > logs_9/M2.log 2>&1 &
java -cp ./java CouncilMember M3 --config ../network.config --profile reliable --port 9003 --control-port 10003 > logs_9/M3.log 2>&1 &
java -cp ./java CouncilMember M4 --config ../network.config --profile reliable --port 9004 --control-port 10004 > logs_9/M4.log 2>&1 &
java -cp ./java CouncilMember M5 --config ../network.config --profile reliable --port 9005 --control-port 10005 > logs_9/M5.log 2>&1 &
java -cp ./java CouncilMember M6 --config ../network.config --profile reliable --port 9006 --control-port 10006 > logs_9/M6.log 2>&1 &
java -cp ./java CouncilMember M7 --config ../network.config --profile reliable --port 9007 --control-port 10007 > logs_9/M7.log 2>&1 &
java -cp ./java CouncilMember M8 --config ../network.config --profile reliable --port 9008 --control-port 10008 > logs_9/M8.log 2>&1 &
java -cp ./java CouncilMember M9 --config ../network.config --profile reliable --port 9009 --control-port 10009 > logs_9/M9.log 2>&1 &
3. M5 make a proposal:
java -cp ./java TriggerClient --host localhost --port 10005 M5 propose M5
4.Check the log:
grep -H "\[CONSENSUS\]" logs_9/*.log
5. Stop all the member:
pkill -f "java .*CouncilMember"
6. Give the script execute permissions
chmod +x run_tests.sh  chmod +x Assignment3/system/run_tests.sh
7. Run the file
./run_tests.sh     Assignment3/system/run_tests.sh

### Screenshot of the Terminal
Scenario 1:

<img width="524" height="66" alt="image" src="https://github.com/user-attachments/assets/0ecca136-db27-451f-a63b-14d8b4ed10e0" />

Scenario 2:

<img width="524" height="119" alt="image" src="https://github.com/user-attachments/assets/c6327d01-f7b1-4b57-8c96-0f0775ea561a" />

Scenario 3:
<img width="524" height="208" alt="image" src="https://github.com/user-attachments/assets/41cb5fdd-213f-4562-9b37-c2600c88b73a" />

