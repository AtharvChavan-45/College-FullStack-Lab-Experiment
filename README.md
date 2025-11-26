# College-FullStack-Lab-Experiment
Source code and documentation for a full-stack web development laboratory experiment completed as a college assignment.

### server.js
```jsx
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');

const app= express();
const PORT= 5000;

//Middleware
app.use(cors());
app.use(express.json());

//connect to mongodb(local)
mongoose.connect('mongodb://localhost:27017/crud_demo')
.then(()=> console.log('Mongodb connected'))
.catch(err => console.log(err)

);

//simple user model inside same file

const User = mongoose.model('User',new mongoose.Schema({
    name:String,
    email:String,
    age:Number
}))

// create user
app.post('/users',async(req,res)=>{
    try{
        const user = await User.create(req.body);
        res.json(user);
    }catch(err){
        res.status(400).json({error:err.message});
    }
});

//read all user
app.get('/users', async(req,res)=>{
    const users=await User.find();
    res.json(users);
});

//update user
app.put('/users/:id',async(req,res)=>{
    try{
        const updated=await User.findByIdAndUpdate(req.params.id,req.body,{new:true});
        res.json(updated);
        
    }catch(err){
        res.status(400).json({error:err.message});
    }
})

//delete user
app.delete('users/:id' ,async(req,res)=>{
    await User.findByIdAndDelete(req.params.id);
    res.json({message:'Deleted'});
});

app.listen(PORT,()=>
console.log(`Server running on http://localhost:${PORT}`)
);
```
### App.js

```jsx
import './App.css';
import React from "react";
import {BrowserRouter as Router,Routes,Route,Link} from "react-router-dom"
import Home from './components/Home/Home';
import About from './components/About/About';
import AddUser from './components/AddUser/AddUser';
import UpdateUser from './components/UpdateUser/UpdateUser';
function App() {
  return (
   <Router>
    <div>
      <nav>
        <Link to="/" style={{margin:10}}>Home</Link>
        <Link to="/about" style={{margin:10}}>About</Link>
        <Link to="/adduser" style={{margin:10}}>Add User</Link>
        <Link to="/update" style={{margin:10}}>Update User</Link>
      </nav>
      <Routes>
        <Route path="/" element={<Home/>}/>
        <Route path="/about" element={<About/>}/>
        <Route path="/adduser" element={<AddUser/>}/>
        <Route path="/update" element={<UpdateUser/>}/>
      </Routes>
    </div>
   </Router>
  );
}

export default App;
```
### Add item
 
 ```jsx
 import React,{useState} from 'react'

function AddUser() {
    const[name,setName] = useState("");
    const[email,setEmail] = useState("");
    const [age,setAge] = useState("");

    const handleSubmit = async (e)=>{
        e.preventDefault();

        const user = {name,email,age};

        await fetch('http://localhost:5000/users',{
            method:'POST',
            headers:{"Content-Type":"application/json"},
            body:JSON.stringify(user)
        });
        alert("User saved!");
    };

  return (
    <div>
      <h2>Add User</h2>
      <form onSubmit={handleSubmit}>
        <input 
            placeholder="Name"
            value={name}
            onChange={(e)=>setName(e.target.value)} 
            
        /><br/><br/>

        <input 
        placeholder="Email"
        value={email}
        onChange={(e)=>setEmail(e.target.value)}
         /><br/><br/>

         <input 
         placeholder='age'
         value={age}
         onChange={(e)=>setAge(e.target.value)}
          />

          <button type="Submit">Save</button>
      </form>
    </div>
  )
}

export default AddUser;

 ```

 ### Update item

 ```jsx

import React,{useState} from 'react';


function UpdateUser() {
    const [id,setId] = useState("");
    const [name,setName] = useState("");
    const [email,setEmail] = useState("");
    const [age,setAge] = useState("");

    const updateUser = async () =>{
        const updatedData ={name,email,age};

        const res = await fetch(`http://localhost:5000/users/${id}`,{
            method:"PUT",
            headers:{"Content-Type":"application/json"},
            body:JSON.stringify(updatedData),
        });
        const data = await res.json();
        console.log(data);
        alert("User updated!");
    };
  return (
    <div>
        <h2>Update User</h2>

      <input
        placeholder="User ID"
        onChange={(e) => setId(e.target.value)}
      /><br/><br/>

      <input
        placeholder="New Name"
        onChange={(e) => setName(e.target.value)}
      /><br/><br/>

      <input
        placeholder="New Email"
        onChange={(e) => setEmail(e.target.value)}
      /><br/><br/>

      <input
        placeholder="New Age"
        onChange={(e) => setAge(e.target.value)}
      /><br/><br/>

      <button onClick={updateUser}>Update User</button>
      
    </div>
  )
}

export default UpdateUser

 ```
