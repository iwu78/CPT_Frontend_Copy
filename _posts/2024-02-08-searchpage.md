<html lang="en">
<p1>Search for designs!</p1>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Search Bar with Toggle Buttons</title>
    <style>
        .toggle-buttons {
            display: inline-block;
        }
        .toggle-buttons button {
            background-color: #ccc;
            border: none;
            color: black;
            padding: 10px 20px;
            text-align: center;
            text-decoration: none;
            display: inline-block;
            font-size: 16px;
            margin: 4px 2px;
            cursor: pointer;
            border-radius: 4px;
        }
        
        .toggle-buttons button.active {
            background-color: #007bff;
            color: white;
        }
    </style>
</head>
<body>
    <form action="#" method="get" onsubmit="return checkButton()">
        <input type="text" name="search" id="search" style="width: 400px;" placeholder="Enter your search term">
        <button type="submit">Search</button>
        <div class="toggle-buttons">
            <button id="publicBtn" type="button" onclick="toggleButtons('publicBtn')">Public</button>
            <button id="privateBtn" type="button" onclick="toggleButtons('privateBtn')">Private</button>
        </div>
    </form>
    <div id="tableContainer"></div>
    <script>
        var ian;
        function toggleButtons(activeButtonId) {
            var buttons = document.querySelectorAll('.toggle-buttons button');
            buttons.forEach(function(button) {
                if (button.id === activeButtonId) {
                    button.classList.add('active');
                } else {
                    button.classList.remove('active');
                }
            });
        }
        function getSearchTerm() {
            return document.getElementById('search').value.trim();
        }
        function checkButton() {
            var publicBtn = document.getElementById('publicBtn');
            var isPublicActive = publicBtn.classList.contains('active');
            var searchTerm = document.getElementById('search').value.trim();
            console.log(searchTerm);
            if (isPublicActive) {
                ian = "public";
                getPublic();
            } else {
                ian = "private";
                getPrivate();
            }
            console.log(ian); // troubleshooting
            return false; // Prevent form submission for demonstration purposes
        }
        function getPublic() {
            // Making the GET request (public)
            fetch('http://127.0.0.1:8086/api/users/search')
                .then(response => {
                    if (!response.ok) {
                        throw new Error('Network response was not ok');
                    }
                    return response.json();
                })
                .then(data => {
                    console.log(data); // Handle the data returned from the server
                    displayDataInTable(data.Designs);
                })
                .catch(error => {
                    console.error('There was a problem with the fetch operation:', error);
                });
        }
//        function getAuthToken() {
            // Retrieve the authentication token from cookies
//            return document.cookie.replace(/(?:(?:^|.*;\s*)jwt\s*=\s*([^;]*).*$)|^.*$/, "$1");
//}
        function getPrivate() {
            // Making the GET request (private)
            // MUST UPDATE LATER!!!
            //
            //
            const authOptions = {
                mode: 'cors', // no-cors, *cors, same-origin
                credentials: 'include', // include, same-origin, omit
                headers: {
                    'Content-Type': 'application/json',
                },
                method: 'PUT', // Override the method property
            };
            fetch('http://127.0.0.1:8086/api/users/search', authOptions)
            .then(response => {
                // handle error response from Web API
                if (!response.ok) {
                    const errorMsg = 'there was a skill issue ' + response.status;
                    console.log(errorMsg);
                    return;
                }
                displayDataInTable(data.Designs);
            })
        }
        function displayDataInTable(data) {
            var tableContainer = document.getElementById('tableContainer');
            var tableHTML = '<table>';
            const searchTerm = getSearchTerm();
            tableHTML += '<tr><th>Name</th><th>Image</th><th>Content</th><th>Likes</th><th>Dislikes</th><th>Type</th></tr>';
            data.forEach(function(item) {
                // Check if searchTerm is a substring of Name or Content
                if (searchTerm && (item.Name.includes(searchTerm) || (item.Content && item.Content.includes(searchTerm)))) {
                    tableHTML += '<tr>';
                    tableHTML += '<td>' + item.Name + '</td>';
                    tableHTML += '<td></td>'; // for the image
                    tableHTML += '<td>' + (item.Content || '') + '</td>';
                    tableHTML += '<td>' + item.Likes + '</td>';
                    tableHTML += '<td>' + item.Dislikes + '</td>';
                    tableHTML += '<td>' + item.Type + '</td>';
                    tableHTML += '</tr>';
                }
            });
            tableHTML += '</table>';
            tableContainer.innerHTML = tableHTML;
        }
        document.getElementById('search').addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                checkButton();
            }
        });
    </script>