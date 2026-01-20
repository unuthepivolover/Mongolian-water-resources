<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mongolia Water Resources</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap');
        
        * {
            font-family: 'Poppins', sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
        }
        
        #map {
            height: 600px;
            border-radius: 12px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
        }
        
        .leaflet-popup-content-wrapper {
            border-radius: 8px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.15);
        }
        
        .filter-btn {
            transition: all 0.3s ease;
            cursor: pointer;
        }
        
        .filter-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 12px rgba(0,0,0,0.2);
        }
        
        .filter-btn.active {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }
        
        .water-card {
            transition: all 0.3s ease;
            cursor: pointer;
        }
        
        .water-card:hover {
            transform: translateY(-4px);
            box-shadow: 0 8px 24px rgba(0,0,0,0.15);
        }
        
        .hero-section {
            background: linear-gradient(135deg, rgba(102, 126, 234, 0.9) 0%, rgba(118, 75, 162, 0.9) 100%);
            backdrop-filter: blur(10px);
        }
        
        .stat-card {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.3);
        }
        
        .table-container {
            max-height: 500px;
            overflow-y: auto;
            border-radius: 12px;
        }
        
        .table-container::-webkit-scrollbar {
            width: 8px;
        }
        
        .table-container::-webkit-scrollbar-track {
            background: #f1f1f1;
            border-radius: 10px;
        }
        
        .table-container::-webkit-scrollbar-thumb {
            background: #667eea;
            border-radius: 10px;
        }
        
        .badge {
            display: inline-block;
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 0.75rem;
            font-weight: 600;
        }
        
        .badge-river { background: #3b82f6; color: white; }
        .badge-lake { background: #06b6d4; color: white; }
        .badge-spring { background: #10b981; color: white; }
        .badge-hot-spring { background: #f59e0b; color: white; }
        
        .badge-safe { background: #22c55e; color: white; }
        .badge-treat { background: #eab308; color: white; }
        .badge-not-safe { background: #ef4444; color: white; }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .fade-in {
            animation: fadeIn 0.6s ease-out;
        }
    </style>
</head>
<body class="p-4 md:p-8">
    <!-- Hero Section -->
    <div class="hero-section rounded-2xl p-8 md:p-12 mb-8 shadow-2xl fade-in">
        <h1 class="text-4xl md:text-6xl font-bold text-white mb-4">🇲🇳 Mongolia Water Resources</h1>
        <p class="text-xl text-white opacity-90 mb-6">Explore Mongolia's pristine rivers, lakes, springs, and mineral waters across all provinces</p>
        
        <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mt-8">
            <div class="stat-card rounded-xl p-6 text-center">
                <div class="text-3xl font-bold text-blue-600" id="riverCount">0</div>
                <div class="text-sm text-gray-600 mt-1">Rivers</div>
            </div>
            <div class="stat-card rounded-xl p-6 text-center">
                <div class="text-3xl font-bold text-cyan-600" id="lakeCount">0</div>
                <div class="text-sm text-gray-600 mt-1">Lakes</div>
            </div>
            <div class="stat-card rounded-xl p-6 text-center">
                <div class="text-3xl font-bold text-green-600" id="springCount">0</div>
                <div class="text-sm text-gray-600 mt-1">Springs</div>
            </div>
            <div class="stat-card rounded-xl p-6 text-center">
                <div class="text-3xl font-bold text-orange-600" id="hotSpringCount">0</div>
                <div class="text-sm text-gray-600 mt-1">Hot Springs</div>
            </div>
        </div>
    </div>

    <!-- Filters Section -->
    <div class="bg-white rounded-2xl p-6 mb-8 shadow-xl fade-in">
        <h2 class="text-2xl font-bold text-gray-800 mb-4">🔍 Filter Water Sources</h2>
        
        <div class="mb-4">
            <h3 class="text-sm font-semibold text-gray-600 mb-2">By Type:</h3>
            <div class="flex flex-wrap gap-2">
                <button class="filter-btn active bg-white px-4 py-2 rounded-lg border-2 border-gray-200" data-filter-type="all" data-category="type">All Types</button>
                <button class="filter-btn bg-white px-4 py-2 rounded-lg border-2 border-gray-200" data-filter-type="River" data-category="type">🏞️ Rivers</button>
                <button class="filter-btn bg-white px-4 py-2 rounded-lg border-2 border-gray-200" data-filter-type="Lake" data-category="type">🌊 Lakes</button>
                <button class="filter-btn bg-white px-4 py-2 rounded-lg border-2 border-gray-200" data-filter-type="Spring" data-category="type">💧 Springs</button>
                <button class="filter-btn bg-white px-4 py-2 rounded-lg border-2 border-gray-200" data-filter-type="Hot Spring" data-category="type">♨️ Hot/Mineral Springs</button>
            </div>
        </div>
        
        <div>
            <h3 class="text-sm font-semibold text-gray-600 mb-2">By Drinkability:</h3>
            <div class="flex flex-wrap gap-2">
                <button class="filter-btn active bg-white px-4 py-2 rounded-lg border-2 border-gray-200" data-filter-type="all" data-category="drinkability">All</button>
                <button class="filter-btn bg-white px-4 py-2 rounded-lg border-2 border-gray-200" data-filter-type="Safe" data-category="drinkability">✅ Safe to Drink</button>
                <button class="filter-btn bg-white px-4 py-2 rounded-lg border-2 border-gray-200" data-filter-type="Treat" data-category="drinkability">⚠️ Needs Treatment</button>
                <button class="filter-btn bg-white px-4 py-2 rounded-lg border-2 border-gray-200" data-filter-type="Not Safe" data-category="drinkability">🚫 Not Safe</button>
            </div>
        </div>
    </div>

    <!-- Map Section -->
    <div class="bg-white rounded-2xl p-6 mb-8 shadow-xl fade-in">
        <h2 class="text-2xl font-bold text-gray-800 mb-4">🗺️ Interactive Water Resources Map</h2>
        <div id="map"></div>
    </div>

    <!-- Data Table Section -->
    <div class="bg-white rounded-2xl p-6 shadow-xl fade-in">
        <h2 class="text-2xl font-bold text-gray-800 mb-4">📊 Complete Water Resources Database</h2>
        <div class="table-container">
            <table class="w-full text-sm" id="waterTable">
                <thead class="bg-gradient-to-r from-purple-600 to-indigo-600 text-white sticky top-0">
                    <tr>
                        <th class="p-3 text-left">Name</th>
                        <th class="p-3 text-left">Type</th>
                        <th class="p-3 text-left">Province</th>
                        <th class="p-3 text-left">Drinkability</th>
                        <th class="p-3 text-left">Recommendation</th>
                        <th class="p-3 text-left">Notes</th>
                    </tr>
                </thead>
                <tbody id="tableBody" class="divide-y divide-gray-200">
                </tbody>
            </table>
        </div>
    </div>

    <script>
        // Comprehensive water resources data for Mongolia
        const waterResources = [
            // Rivers
            { name: "Selenge River", type: "River", province: "Selenge", lat: 49.7, lng: 106.5, drinkability: "Treat", recommendation: "Boil or filter before drinking", notes: "Mongolia's largest river, vital for local communities and biodiversity" },
            { name: "Orkhon River", type: "River", province: "Arkhangai", lat: 47.3, lng: 102.8, drinkability: "Treat", recommendation: "Boil or filter before drinking", notes: "UNESCO World Heritage site, culturally significant waterway" },
            { name: "Tuul River", type: "River", province: "Töv", lat: 47.9, lng: 107.0, drinkability: "Not Safe", recommendation: "Not suitable for drinking, recreational use only", notes: "Flows through Ulaanbaatar, popular for recreation" },
            { name: "Kherlen River", type: "River", province: "Khentii", lat: 47.3, lng: 108.7, drinkability: "Treat", recommendation: "Boil or filter before drinking", notes: "Important river in eastern Mongolia, supports local agriculture" },
            { name: "Zavkhan River", type: "River", province: "Zavkhan", lat: 48.5, lng: 96.8, drinkability: "Safe", recommendation: "Generally safe, locals drink directly", notes: "Remote river with excellent water quality" },
            { name: "Eg River", type: "River", province: "Arkhangai", lat: 48.8, lng: 100.2, drinkability: "Treat", recommendation: "Boil before drinking", notes: "Tributary of Selenge, popular fishing destination" },
            
            // Lakes
            { name: "Khövsgöl Lake", type: "Lake", province: "Khövsgöl", lat: 51.0, lng: 100.5, drinkability: "Safe", recommendation: "Safe to drink, pristine water", notes: "Mongolia's deepest lake, 'Blue Pearl of Mongolia', major tourist attraction" },
            { name: "Uvs Lake", type: "Lake", province: "Uvs", lat: 50.3, lng: 92.8, drinkability: "Not Safe", recommendation: "Saline water, not for drinking", notes: "Largest lake in Mongolia, saline water, UNESCO biosphere reserve" },
            { name: "Buir Lake", type: "Lake", province: "Dornod", lat: 47.9, lng: 117.7, drinkability: "Treat", recommendation: "Filter and boil before drinking", notes: "International lake shared with China, important for migratory birds" },
            { name: "Khyargas Lake", type: "Lake", province: "Uvs", lat: 49.1, lng: 93.3, drinkability: "Not Safe", recommendation: "Saline water, bathing only", notes: "Saline lake, popular for therapeutic bathing" },
            { name: "Achit Lake", type: "Lake", province: "Bayan-Ölgii", lat: 49.4, lng: 90.5, drinkability: "Treat", recommendation: "Boil before drinking", notes: "Remote mountain lake, pristine environment" },
            { name: "Dörgön Lake", type: "Lake", province: "Uvs", lat: 49.6, lng: 92.1, drinkability: "Not Safe", recommendation: "Not for drinking", notes: "Saline lake in western Mongolia" },
            { name: "Ganga Lake", type: "Lake", province: "Sükhbaatar", lat: 46.4, lng: 112.6, drinkability: "Treat", recommendation: "Boil and filter", notes: "Eastern steppe lake, seasonal water levels" },
            { name: "Telmen Lake", type: "Lake", province: "Zavkhan", lat: 48.9, lng: 97.4, drinkability: "Safe", recommendation: "Generally safe to drink", notes: "Freshwater lake in remote area" },
            
            // Springs
            { name: "Ulaan Tsutgalan Spring", type: "Spring", province: "Arkhangai", lat: 47.8, lng: 101.9, drinkability: "Safe", recommendation: "Safe to drink directly", notes: "Near Orkhon Waterfall, pure mountain spring" },
            { name: "Tariat Spring", type: "Spring", province: "Arkhangai", lat: 48.2, lng: 99.7, drinkability: "Safe", recommendation: "Excellent drinking water", notes: "High-quality spring water, locally cherished" },
            { name: "Khustai Spring", type: "Spring", province: "Töv", lat: 47.7, lng: 106.3, drinkability: "Safe", recommendation: "Safe to drink", notes: "Located in Khustai National Park, supports wild horses" },
            { name: "Terelj Spring", type: "Spring", province: "Töv", lat: 47.9, lng: 107.4, drinkability: "Safe", recommendation: "Pure spring water", notes: "Popular tourist area spring, very clean" },
            { name: "Altai Spring", type: "Spring", province: "Bayan-Ölgii", lat: 48.5, lng: 89.6, drinkability: "Safe", recommendation: "Safe to drink", notes: "Mountain spring in Altai region" },
            { name: "Kharkhiraa Spring", type: "Spring", province: "Uvs", lat: 49.3, lng: 91.8, drinkability: "Safe", recommendation: "Excellent water quality", notes: "Remote mountain spring" },
            { name: "Delger Spring", type: "Spring", province: "Dundgovi", lat: 45.8, lng: 106.9, drinkability: "Treat", recommendation: "Boil before drinking", notes: "Desert spring, vital for nomads" },
            { name: "Bayankhongor Spring", type: "Spring", province: "Bayankhongor", lat: 46.2, lng: 100.7, drinkability: "Safe", recommendation: "Safe to drink", notes: "Important water source for local community" },
            
            // Hot/Mineral Springs
            { name: "Tsenkher Hot Spring", type: "Hot Spring", province: "Arkhangai", lat: 47.4, lng: 101.5, drinkability: "Not Safe", recommendation: "Bathing only, therapeutic properties", notes: "Popular spa destination, 86°C water with minerals, treats joint pain" },
            { name: "Shargaljuut Hot Spring", type: "Hot Spring", province: "Arkhangai", lat: 47.9, lng: 101.1, drinkability: "Not Safe", recommendation: "Bathing only", notes: "Remote hot spring, therapeutic bathing, 50°C" },
            { name: "Khujirt Hot Spring", type: "Hot Spring", province: "Övörkhangai", lat: 46.9, lng: 102.8, drinkability: "Not Safe", recommendation: "Therapeutic bathing only", notes: "Health resort, treats digestive and joint ailments" },
            { name: "Yestii Hot Spring", type: "Hot Spring", province: "Khentii", lat: 47.6, lng: 107.8, drinkability: "Not Safe", recommendation: "Bathing only", notes: "Natural hot spring in forested area, 45°C" },
            { name: "Buurug Spring", type: "Hot Spring", province: "Selenge", lat: 49.1, lng: 106.8, drinkability: "Not Safe", recommendation: "Bathing and spa treatments", notes: "Mineral spring with healing properties" },
            { name: "Rashaan Hot Spring", type: "Hot Spring", province: "Khövsgöl", lat: 50.2, lng: 99.7, drinkability: "Not Safe", recommendation: "Bathing only", notes: "High-altitude hot spring, therapeutic minerals" },
            { name: "Chandmani Mineral Spring", type: "Hot Spring", province: "Khovd", lat: 48.1, lng: 92.3, drinkability: "Not Safe", recommendation: "External use only", notes: "Mineral-rich spring, local spa" },
            { name: "Otgontenger Mineral Spring", type: "Hot Spring", province: "Zavkhan", lat: 48.0, lng: 97.0, drinkability: "Not Safe", recommendation: "Therapeutic bathing", notes: "Sacred mountain area, healing waters" },
            { name: "Ganga Hot Spring", type: "Hot Spring", province: "Sükhbaatar", lat: 46.5, lng: 112.8, drinkability: "Not Safe", recommendation: "Bathing only", notes: "Eastern Mongolia hot spring, 55°C" },
            { name: "Ikh Tamir Mineral Spring", type: "Hot Spring", province: "Arkhangai", lat: 47.1, lng: 101.2, drinkability: "Not Safe", recommendation: "External therapeutic use", notes: "Rich in minerals, popular health resort" },
            { name: "Shar Us Hot Spring", type: "Hot Spring", province: "Uvs", lat: 49.8, lng: 92.6, drinkability: "Not Safe", recommendation: "Bathing only", notes: "Remote hot spring in western Mongolia" },
            { name: "Bayan-Ovoo Mineral Spring", type: "Hot Spring", province: "Bayankhongor", lat: 45.7, lng: 100.9, drinkability: "Not Safe", recommendation: "Therapeutic bathing", notes: "Mineral spring resort, treats various ailments" }
        ];

        // Initialize map
        const map = L.map('map').setView([46.8625, 103.8467], 6);
        
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        // Custom marker icons
        const getMarkerIcon = (type) => {
            const colors = {
                'River': '#3b82f6',
                'Lake': '#06b6d4',
                'Spring': '#10b981',
                'Hot Spring': '#f59e0b'
            };
            
            return L.divIcon({
                className: 'custom-marker',
                html: `<div style="background-color: ${colors[type]}; width: 30px; height: 30px; border-radius: 50%; border: 3px solid white; box-shadow: 0 2px 8px rgba(0,0,0,0.3); display: flex; align-items: center; justify-content: center; color: white; font-weight: bold;">${type[0]}</div>`,
                iconSize: [30, 30]
            });
        };

        // Store markers for filtering
        const markers = [];
        let activeFilters = { type: 'all', drinkability: 'all' };

        // Create markers and table rows
        waterResources.forEach((resource, index) => {
            const marker = L.marker([resource.lat, resource.lng], {
                icon: getMarkerIcon(resource.type)
            }).addTo(map);

            const drinkabilityColor = resource.drinkability === 'Safe' ? '#22c55e' : 
                                     resource.drinkability === 'Treat' ? '#eab308' : '#ef4444';

            marker.bindPopup(`
                <div style="min-width: 250px;">
                    <h3 style="font-size: 1.1rem; font-weight: 700; color: #1f2937; margin-bottom: 8px;">${resource.name}</h3>
                    <div style="margin-bottom: 6px;">
                        <span class="badge badge-${resource.type.toLowerCase().replace(' ', '-')}">${resource.type}</span>
                        <span class="badge badge-${resource.drinkability.toLowerCase().replace(' ', '-')}" style="margin-left: 4px;">${resource.drinkability}</span>
                    </div>
                    <p style="margin: 4px 0; font-size: 0.875rem;"><strong>Province:</strong> ${resource.province}</p>
                    <p style="margin: 4px 0; font-size: 0.875rem;"><strong>Recommendation:</strong> ${resource.recommendation}</p>
                    <p style="margin: 8px 0 0; font-size: 0.875rem; color: #6b7280; font-style: italic;">${resource.notes}</p>
                </div>
            `);

            markers.push({ marker, resource });
        });

        // Populate table
        const tableBody = document.getElementById('tableBody');
        const updateTable = () => {
            tableBody.innerHTML = '';
            const filteredResources = waterResources.filter(resource => {
                const typeMatch = activeFilters.type === 'all' || resource.type === activeFilters.type;
                const drinkMatch = activeFilters.drinkability === 'all' || resource.drinkability === activeFilters.drinkability;
                return typeMatch && drinkMatch;
            });

            filteredResources.forEach((resource, index) => {
                const row = document.createElement('tr');
                row.className = 'hover:bg-gray-50 transition-colors';
                row.innerHTML = `
                    <td class="p-3 font-semibold text-gray-800">${resource.name}</td>
                    <td class="p-3"><span class="badge badge-${resource.type.toLowerCase().replace(' ', '-')}">${resource.type}</span></td>
                    <td class="p-3 text-gray-600">${resource.province}</td>
                    <td class="p-3"><span class="badge badge-${resource.drinkability.toLowerCase().replace(' ', '-')}">${resource.drinkability}</span></td>
                    <td class="p-3 text-gray-700">${resource.recommendation}</td>
                    <td class="p-3 text-gray-600 text-xs">${resource.notes}</td>
                `;
                tableBody.appendChild(row);
            });
        };

        updateTable();

        // Update statistics
        const updateStats = () => {
            document.getElementById('riverCount').textContent = waterResources.filter(r => r.type === 'River').length;
            document.getElementById('lakeCount').textContent = waterResources.filter(r => r.type === 'Lake').length;
            document.getElementById('springCount').textContent = waterResources.filter(r => r.type === 'Spring').length;
            document.getElementById('hotSpringCount').textContent = waterResources.filter(r => r.type === 'Hot Spring').length;
        };
        updateStats();

        // Filter functionality
        document.querySelectorAll('.filter-btn').forEach(btn => {
            btn.addEventListener('click', function() {
                const category = this.dataset.category;
                const filterType = this.dataset.filterType;
                
                // Update active state
                document.querySelectorAll(`[data-category="${category}"]`).forEach(b => b.classList.remove('active'));
                this.classList.add('active');
                
                // Update filters
                activeFilters[category] = filterType;
                
                // Filter markers
                markers.forEach(({ marker, resource }) => {
                    const typeMatch = activeFilters.type === 'all' || resource.type === activeFilters.type;
                    const drinkMatch = activeFilters.drinkability === 'all' || resource.drinkability === activeFilters.drinkability;
                    
                    if (typeMatch && drinkMatch) {
                        marker.addTo(map);
                    } else {
                        map.removeLayer(marker);
                    }
                });
                
                // Update table
                updateTable();
            });
        });
    </script>
</body>
</html># Mongolian-water-resources
Water resource information with interactive map
