<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Table to JSON</title>
    <style>
        #jsonOutput {
            width: 100%;
            height: 200px;
        }
    </style>
</head>
<body>
    <table id="myTable">
        <thead>
            <tr>
                <th contenteditable="true">Name</th>
                <th contenteditable="true">Age</th>
                <th contenteditable="true">Country</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td contenteditable="true">John Doe</td>
                <td contenteditable="true">30</td>
                <td contenteditable="true">USA</td>
            </tr>
            <tr>
                <td contenteditable="true">Jane Smith</td>
                <td contenteditable="true">25</td>
                <td contenteditable="true">Canada</td>
            </tr>
        </tbody>
    </table>
    <button id="exportBtn">Export to JSON</button>
    <button id="importBtn">Import from JSON</button>
    <textarea id="jsonOutput" readonly></textarea>

    <script>
        document.getElementById('exportBtn').addEventListener('click', () => {
            const table = document.getElementById('myTable');
            const headers = Array.from(table.querySelectorAll('th')).map(th => th.textContent);
            const rows = Array.from(table.querySelectorAll('tbody tr')).map(tr => {
                const cells = Array.from(tr.querySelectorAll('td'));
                const rowData = {};
                cells.forEach((cell, index) => {
                    rowData[headers[index]] = cell.textContent;
                });
                return rowData;
            });

            const jsonString = JSON.stringify(rows, null, 2);
            document.getElementById('jsonOutput').value = jsonString;

            // Create or update the data.js script tag
            let scriptTag = document.getElementById('data-js');
            if (!scriptTag) {
                scriptTag = document.createElement('script');
                scriptTag.id = 'data-js';
                scriptTag.type = 'text/javascript';
                document.body.appendChild(scriptTag);
            }
            scriptTag.textContent = `const data = ${jsonString};`;
        });

        document.getElementById('importBtn').addEventListener('click', () => {
            fetch('data.json')
                .then(response => response.json())
                .then(data => {
                    const tableBody = document.getElementById('myTable').querySelector('tbody');
                    tableBody.innerHTML = ''; // Clear existing rows

                    data.forEach(rowData => {
                        const row = document.createElement('tr');
                        Object.values(rowData).forEach(cellData => {
                            const cell = document.createElement('td');
                            cell.textContent = cellData;
                            row.appendChild(cell);
                        });
                        tableBody.appendChild(row);
                    });
                })
                .catch(error => console.error('Error loading JSON:', error));
        });
    </script>
</body>
</html>
