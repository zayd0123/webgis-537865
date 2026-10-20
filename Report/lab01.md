Name: Zaid Younas  
Roll: 537865  
Repository: https://github.com/zayd0123/webgis-537865  
Live site: https://zayd0123.github.io/webgis-537865/


Questions:

Q1. In Part 2 your page made one network request. After Part 4 it made dozens. Explain in two or three sentences what changed and why.
A: In Part 2, the page was only HTML and CSS written inside the file itself, so the browser needed just one request for index.html. After Part 4, Leaflet is loaded from an external CDN and the map tiles are fetched from OpenStreetMap one square at a time, so each visible tile becomes its own HTTP request. Panning or zooming triggers new tile requests, which is why the count grows into the dozens.

Q2. What is the difference between what HTML does and what CSS does? Give one example of each from your own file.
HTML defines the structure and content of the page — what exists. CSS defines the appearance — how it looks. In my file, <h1>Islamabad</h1> is HTML (it creates the heading), and h1 { color:#0b2545; } is CSS (it colours that heading dark blue).

Q3. Why does the #map rule need a height, when the h1 rule does not?
An <h1> contains text, so its height is determined automatically by the size of that text. The <div id="map"> is empty — it has no content to give it size — so without an explicit height it collapses to zero pixels and the map has nowhere to draw. Setting height: 480px gives the container a real box for Leaflet to fill.

Q4. You opened your page through Live Server at 127.0.0.1 instead of double-clicking the file. Give one reason this matters.
When a file is opened directly, the browser uses the file:// protocol and blocks requests to external data files for security reasons. Live Server serves the page over http://, exactly like a real web server, so tiles, GeoJSON files, and API calls behave the same locally as they will once published.

Q5. A classmate's marker appears in the sea near Africa instead of in Islamabad. What is almost certainly wrong, and how would you fix it?
Latitude and longitude are swapped. The coordinates [33.64, 72.99] were probably written as [72.99, 33.64], which places the point in the ocean off the coast of Africa. Leaflet expects [latitude, longitude], so swapping the two numbers back fixes it.

