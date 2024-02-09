---
comments: True
layout: post
toc: false
title: Dashboard
permalink: /dashboard
type: hacks
courses: { "compsci": { "week": 2 } }
---
<html>
<head> 
    <title>BlueJay</title>
    <style>
    html, body {
        height:100%;
        margin: 0;
        padding: 0;
        overflow: hidden;
    }    

    p {
        color: white;
    }
    #response {
        height : 100%;
        overflow-y: scroll;
        width:100%;
        word-wrap:break-word;
        overflow-x: hidden;
    }
    body {
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        margin: 0;
        padding: 0;
        background-color: #424549;
    } 
    #chat-container {
        display: flex;
        flex-direction: column;
        justify-content: flex-end;
        height: 80%; /* Adjust the height as needed */
        border: 1px solid #ccc;
        background-color: #282b30;   
        padding: 10px;
    }
    #messages {
        flex-grow: 1;
    }
    #input-container {
        display: flex;
        background-color: #282b30;
        padding: 10px;
    }
    #message {
        border-radius: 15px;
        flex-grow: 1;
        padding: 5px;
        font-size: 16px;
        height: 90%;
        background-color: #36393e;
        color: white;
        border: none;
    }
    #send-button {
        background-color: #7289da;
        color: #fff;
        border: none;
        padding: 10px 20px;
        cursor: pointer;
    }
    .content {
        padding-left: 10%; /* Adjust this value based on your sidebar width */
        width: 90%; /* Takes up the remaining width */
        overflow: hidden;
        height:100%;
    }
    /* Hide the default scrollbar */
    ::-webkit-scrollbar {
        width: 8px;
    }
    
    /* Track (the area behind the thumb) */
    ::-webkit-scrollbar-track {
        background: #36393f; /* Discord's background color */
    }
    
    /* Thumb (the draggable scrolling handle) */
    ::-webkit-scrollbar-thumb {
        background: #7289da; /* Discord's primary color */
        border-radius: 4px;
    }
    
    /* On hover, make the thumb slightly brighter */
    ::-webkit-scrollbar-thumb:hover {
        background: #677bc4; /* A slightly lighter color on hover */
    } 
</style>
</head>
<body onload="scrollDown()">
    <div id="navbar-container"></div>
    <script>
        const navbarContainer = document.getElementById("navbar-container");
        const xhttp = new XMLHttpRequest();
        xhttp.onreadystatechange = function() {
            if (this.readyState == 4 && this.status == 200) {
                navbarContainer.innerHTML = this.responseText;
            }
        };
        xhttp.open("GET", "/navbar.html", true);
        xhttp.send();
        
    </script>
    <div class="content">
        <div id="chat-container">
            <p id="response"></p>
            <div id="messages"></div>
        </div>
        <div id="input-container">
            <input name="text" type="text" id="message" placeholder="Type your message..." value="">
            <button id="send-button">Send</button>
        </div>
        <button onclick=logout()> Logout </button>
    </div>
    
    <script>
        var username = localStorage.getItem("usernameData")
        console.log(username)
        var flag = localStorage.getItem("flagData")
        if (flag != 1){
            window.location.replace("./login.html")
        }
        const sendButton = document.getElementById("send-button");
        function logout() { 
            flag = 0
            window.location.replace("./login.html")
        }
        // Function to add a new message to the chat
        function addMessage(message) {
            const timestamp = getCurrentTime();
            const messageElement = document.createElement("p");
            messageElement.textContent = `[${timestamp}] ${message}`;
            messagesDiv.appendChild(messageElement);
        }
        function getCurrentTime() {
            const now = new Date();
            const hours = now.getHours();
            const minutes = now.getMinutes().toString().padStart(2, "0");
            const seconds = now.getSeconds().toString().padStart(2, "0");
            const ampm = hours >= 12 ? 'PM' : 'AM';
            const date =  now.getMonth() + "/" + now.getDay() + "/" + now.getFullYear();
            const formattedHours = hours % 12 || 12;
            return `${date} ${formattedHours}:${minutes} ${ampm}`;
        }


        // brings the chat to bottom
        function scrollDown(){
            const chatContainer = document.getElementById("response");
            chatContainer.scrollTop = chatContainer.scrollHeight;
        }

        // Function to send a POST request to the backend to store the message
        function sendMessage() {
            const message = document.getElementById("message").value;
            const apiUrl = "https://whispbackend.duckdns.org/messageDB";
            // const apiUrl = "http://127.0.0.1:8086/messageDB"
            

            // Create a data object to send in the POST request
            const data = {
                message: "<p style=font-weight:bold;display:inline>" + username + '</p> &nbsp' + '<p style=color:#808080;display:inline>'+getCurrentTime() + "</p><br>" + "" + '&nbsp -> &nbsp' + message
            };
            document.getElementById("message").value = '';
            // Perform the POST request to store the message
            fetch(apiUrl, {
                method: "POST",
                headers: {
                    "Content-Type": "application/json"
                },
                body: JSON.stringify(data)
            })
            .then(response => {
                if (!response.ok) {
                    throw new Error('Network response was not ok');
                }
                return response.json();
            })
            .then(data => {
                // document.getElementById("response").textContent = "Response: " + JSON.stringify(data.message);
            })
            .catch(error => {
                document.getElementById("response").textContent = "Error: " + error;
            });
        }

        // Function to send a GET request to fetch the message
        // Function to send a GET request to fetch the message
        function getMessage() {
            const apiUrl = "https://whispbackend.duckdns.org/messageDB";
            // const apiUrl = "http://127.0.0.1:8086/messageDB"

            // Perform a GET request to fetch the message
            fetch(apiUrl, {
                method: "GET",
            })
            .then(response => {
                if (!response.ok) {
                    throw new Error('Network response was not ok');
                }
                return response.json();
            })
            .then(data => {
                // document.getElementById("response").value = data.message;
                data = JSON.stringify(data);
                data = JSON.parse(data);
                var tempstring = "";
                for (let i in data){
                    tempstring += data[i] + "<br>";
                }
                document.getElementById("response").innerHTML = tempstring;
            })
            .catch(error => {
                document.getElementById("response").textContent = "Error: " + error;
            });
        }

        // Add an event listener to the "Send Message" button
        // document.getElementById("send-button").addEventListener("click", sendMessage);

        // Add an event listener to the "Get Message" button
        // var t=setInterval(getMessage,1);
        // document.getElementById("get-button").addEventListener("click", getMessage);
        document.getElementById("message").addEventListener("keydown", function(event) {
        if (event.key === "Enter") {
            sendMessage(); // Call the sendMessage function when Enter is pressed
            scrollDown()
        }
        sendButton.addEventListener("click", () => {
            sendMessage();
            scrollDown()
        });
    });
        const autoFocusInput = document.getElementById("message");
        autoFocusInput.focus();
        // Fetch a message from the backend when the page loads
        // window.onload = getMessage;
        //window.onload=scrollDown
        setInterval(getMessage, 100);
    </script>
</body>
</html>