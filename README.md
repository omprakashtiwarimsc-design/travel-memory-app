# travel-memory-app
Travelmemory app captures users data for travelling history and their experience 
My website --> Browse -->  www.aws-ninjas.store --> Application is accessible --> 
Added Data --> Germary --> More Details 

Application is accessible from the my website DNS 

<img width="1470" height="820" alt="Screenshot 2026-10-12 at 1 38 30 PM" src="https://github.com/user-attachments/assets/969db9b9-4257-4565-8e6d-b3e952ce77ca" />
Required setup : 
1. Mongo Atlas as a database. (MERN) , to access db you can user Mongo Compass app.
ENV : MONGO_URI , PORT : 3000

2. Create a free cluster at cloud.mongodb.com.
Database Access → Add New Database User → set username/password (save these).
Network Access → Add IP Address → 0.0.0.0/0 (allow access from anywhere, since EC2 IPs vary).
Connect → Drivers → copy the connection string:

<img width="2820" height="640" alt="image" src="https://github.com/user-attachments/assets/fe70bf0b-4614-4f49-bc71-a1383a48a2a8" />
<img width="1874" height="610" alt="image" src="https://github.com/user-attachments/assets/78d8654c-c4b6-45df-8ed5-92034701df8c" /> 

3. Launch the two EC2 Instance frontend-Mater and Backend-Master
   <img width="1463" height="447" alt="Screenshot 2026-10-12 at 2 20 54 PM" src="https://github.com/user-attachments/assets/5ea7dfd3-5733-4827-bc7d-76444530d4cf" />
  3.a --> 
  |
  |
  -------- Configure the backend master -->
         update package manager -> sudo apt update
         install node js , npm ,git and nginx --> sudo apt install -y nodejs npm git nginx
      --   Clone the travel memory application files --
   ->  git clone https://github.com/UnpredictablePrashant/TravelMemory.git
      --  Install npm --
   ->  npm install
      -- Create the backned env with Mongo UTI details and port --
   ->  cd TravelMemory/backend
       vi .env
       -- add below lines save and exit --
   MONGO_URI=mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/travelmemory?retryWrites=true&w=majority
   PORT=3000
   
   
   
           
  
