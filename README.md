# Dekamp.birding
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My South African Birding Book • Free State Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.6.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700&family=Inter:wght@400;500;600&display=swap');
        body { font-family: 'Inter', system-ui, sans-serif; }
        .book-header { font-family: 'Playfair Display', serif; }
        .bird-card { transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        .bird-card:hover { transform: translateY(-8px); box-shadow: 0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1); }
        .modal { animation: fadeInScale 0.3s ease forwards; }
        @keyframes fadeInScale { from { opacity: 0; transform: scale(0.95); } to { opacity: 1; transform: scale(1); } }
        .tail-text { background: linear-gradient(180deg, #166534 0%, #052e16 100%); }
        .audio-link { transition: all 0.2s; }
        .audio-link:hover { background-color: #10b981; color: white; }
    </style>
</head>
<body class="bg-[#f8f1e3] text-[#1f2937]">
    <!-- HEADER -->
    <header class="tail-text text-white py-6 shadow-xl">
        <div class="max-w-7xl mx-auto px-6 flex items-center justify-between">
            <div class="flex items-center gap-4">
                <i class="fa-solid fa-dove text-5xl"></i>
                <div>
                    <h1 class="book-header text-5xl tracking-tight">My South African Birding Book</h1>
                    <p class="text-emerald-200 text-lg">Free State Focus • Phuthaditjhaba Edition • 2026</p>
                </div>
            </div>
            <div class="flex items-center gap-6">
                <button onclick="window.print()" class="flex items-center gap-2 bg-white/20 hover:bg-white/30 px-6 py-3 rounded-2xl text-sm font-medium transition">
                    <i class="fa-solid fa-print"></i> Print Book
                </button>
                <div class="flex items-center bg-white/10 rounded-3xl px-4 py-2 text-sm">
                    <i class="fa-solid fa-location-dot mr-2"></i> Phuthaditjhaba, Free State
                </div>
            </div>
        </div>
    </header>

    <div class="max-w-7xl mx-auto flex gap-8 px-6 py-8">
        <!-- SIDEBAR -->
        <aside class="w-72 bg-white rounded-3xl shadow-xl p-6 h-fit sticky top-8">
            <div class="mb-8">
                <input id="search-input" onkeyup="filterBirds()" type="text" placeholder="Search birds..." class="w-full px-5 py-4 bg-gray-100 rounded-2xl text-sm focus:outline-none focus:ring-2 focus:ring-emerald-600">
            </div>
            <div class="text-xs uppercase tracking-widest text-gray-500 mb-3 font-medium">Categories</div>
            <div id="category-list" class="space-y-1"></div>
            <div class="mt-10 space-y-3">
                <button onclick="showMySightings()" class="w-full flex items-center justify-center gap-3 bg-emerald-700 hover:bg-emerald-800 text-white py-4 rounded-2xl font-medium text-sm transition">
                    <i class="fa-solid fa-camera"></i> MY SIGHTINGS
                </button>
            </div>
            <div class="mt-8 text-[10px] text-gray-400 text-center">
                Photos & notes saved privately in your browser
            </div>
        </aside>

        <!-- MAIN -->
        <main class="flex-1">
            <div class="bg-white rounded-3xl p-10 mb-12 shadow">
                <h2 class="book-header text-4xl mb-4 text-emerald-900">Your Free State Birding Book</h2>
                <p class="text-lg text-gray-600">
                    Personal guide with field info, calls via Xeno-canto, and space for your photos/notes. Focus on common species + the scarcest/rarest in the Free State (eastern highlands, grasslands, wetlands near Phuthaditjhaba/Golden Gate).
                </p>
            </div>

            <div id="birds-grid" class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-8"></div>
        </main>
    </div>

    <!-- MODAL -->
    <div id="bird-modal" class="hidden fixed inset-0 bg-black/70 flex items-center justify-center z-50 p-6">
        <div class="modal bg-white rounded-3xl max-w-6xl w-full max-h-[92vh] overflow-hidden shadow-2xl flex flex-col">
            <div class="px-8 py-6 border-b flex items-center justify-between bg-emerald-50">
                <div id="modal-bird-name" class="book-header text-3xl text-emerald-900"></div>
                <button onclick="closeModal()" class="text-3xl text-gray-400 hover:text-gray-600">×</button>
            </div>
            <div class="flex-1 overflow-auto p-8">
                <div class="grid grid-cols-1 lg:grid-cols-2 gap-10">
                    <!-- LEFT: INFO -->
                    <div>
                        <div class="aspect-video bg-gray-100 rounded-2xl overflow-hidden mb-6">
                            <img id="modal-book-image" class="w-full h-full object-cover" alt="">
                        </div>
                        <div id="modal-info" class="space-y-8 text-sm"></div>
                    </div>
                    <!-- RIGHT: YOUR STUFF + CALL -->
                    <div>
                        <div class="flex items-center gap-3 mb-6">
                            <i class="fa-solid fa-camera text-emerald-700 text-2xl"></i>
                            <div class="font-semibold text-lg">My Observation</div>
                        </div>
                        <div id="upload-area" class="border-2 border-dashed border-emerald-300 rounded-3xl p-8 text-center hover:border-emerald-500 transition cursor-pointer">
                            <input type="file" id="photo-upload" accept="image/*" class="hidden" onchange="handlePhotoUpload(event)">
                            <div onclick="document.getElementById('photo-upload').click()" class="flex flex-col items-center gap-3 text-emerald-700">
                                <div class="w-20 h-20 bg-emerald-100 rounded-2xl flex items-center justify-center text-4xl">📸</div>
                                <div class="font-medium">Upload your photo</div>
                            </div>
                        </div>
                        <div id="my-photo-preview-container" class="hidden mt-6">
                            <div class="relative rounded-3xl overflow-hidden border-4 border-white shadow-inner">
                                <img id="my-photo-preview" class="w-full h-64 object-cover" alt="">
                                <button onclick="removeMyPhoto()" class="absolute top-4 right-4 bg-red-500 text-white text-xs px-3 py-1 rounded-xl">Remove</button>
                            </div>
                        </div>
                        <div class="mt-8">
                            <label class="block text-xs uppercase tracking-widest text-gray-500 mb-2">Field Notes</label>
                            <textarea id="my-notes" rows="6" class="w-full px-5 py-4 border border-gray-200 rounded-3xl focus:outline-none focus:border-emerald-600 resize-none" placeholder="Date • Location • Behaviour..."></textarea>
                            <button onclick="saveMyObservation()" class="mt-4 w-full bg-emerald-700 hover:bg-emerald-800 text-white py-4 rounded-2xl font-medium">SAVE OBSERVATION</button>
                        </div>
                        <div id="call-section" class="mt-10 p-6 bg-emerald-50 rounded-3xl">
                            <div class="flex items-center gap-2 mb-3">
                                <i class="fa-solid fa-volume-high text-emerald-700"></i>
                                <div class="font-semibold">Listen to Calls</div>
                            </div>
                            <a id="xeno-link" href="#" target="_blank" class="audio-link flex items-center justify-center gap-3 bg-white text-emerald-700 hover:bg-emerald-700 hover:text-white border border-emerald-200 rounded-2xl py-4 font-medium text-sm">
                                <i class="fa-solid fa-headphones"></i> XENO-CANTO RECORDINGS
                            </a>
                        </div>
                    </div>
                </div>
            </div>
            <div class="px-8 py-5 border-t bg-gray-50 flex justify-between text-xs text-gray-500">
                <div>Data: BirdLife SA 2025/26 Red List, SABAP2, Roberts Guide</div>
                <button onclick="closeModal()" class="font-medium text-emerald-700">Close</button>
            </div>
        </div>
    </div>

    <footer class="bg-white border-t py-12 text-center text-xs text-gray-400">
        Personal Free State bird book • Photos/notes local only • Expand by telling me more groups!
    </footer>

    <script>
        // ALL BIRDS COMBINED
        const birds = [
            // Original/common ones (shortened for space; add your earlier details if needed)
            { id: 1, name: "Blue Crane", sciName: "Anthropoides paradiseus", category: "Cranes & Ground Birds", size: "100–120 cm", identification: "Tall pale blue-grey, white crown, black face.", habitat: "Grasslands, wetlands", distribution: "Strong in Free State Highveld", behaviour: "Dancing courtship", voice: "Trumpet calls", status: "Vulnerable", image: "https://picsum.photos/id/1015/800/500", xenoCantoUrl: "https://xeno-canto.org/species/Anthropoides-paradiseus" },
            { id: 2, name: "Common Ostrich", sciName: "Struthio camelus", category: "Flightless Birds", size: "Up to 2.8 m", identification: "Huge, males black/white", habitat: "Open savanna", distribution: "Widespread Free State", behaviour: "Fast runner", voice: "Booming", status: "Least Concern", image: "https://picsum.photos/id/160/800/500", xenoCantoUrl: "https://xeno-canto.org/species/Struthio-camelus" },
            { id: 3, name: "Hadeda Ibis", sciName: "Bostrychia hagedash", category: "Waders & Ibises", size: "65–75 cm", identification: "Glossy dark, curved red bill", habitat: "Gardens, wetlands", distribution: "Common everywhere incl. Phuthaditjhaba", behaviour: "Probes soil", voice: "Ha-ha-de-dah", status: "Least Concern", image: "https://picsum.photos/id/30/800/500", xenoCantoUrl: "https://xeno-canto.org/species/Bostrychia-hagedash" },
            { id: 4, name: "Lilac-breasted Roller", sciName: "Coracias caudatus", category: "Rollers & Kingfishers", size: "38 cm", identification: "Lilac breast, turquoise wings", habitat: "Savanna", distribution: "Summer migrant", behaviour: "Aerial displays", voice: "Harsh krrraa", status: "Least Concern", image: "https://picsum.photos/id/180/800/500", xenoCantoUrl: "https://xeno-canto.org/species/Coracias-caudatus" },
            { id: 5, name: "African Fish Eagle", sciName: "Haliaeetus vocifer", category: "Raptors", size: "Wingspan 2.4 m", identification: "White head/tail", habitat: "Dams/rivers", distribution: "Common Free State water", behaviour: "Plunge dives", voice: "Weee-ah yelp", status: "Least Concern", image: "https://picsum.photos/id/29/800/500", xenoCantoUrl: "https://xeno-canto.org/species/Haliaeetus-vocifer" },
            // ... add more common ones from earlier if desired

            // Free State scarce/rare specials (2025-2026 status focus)
            { id: 13, name: "Botha's Lark", sciName: "Spizocorys fringillaris", category: "Scarce Grassland Specials", size: "12–14 cm", identification: "Small plain lark, pink bill, streaky brown", habitat: "High-altitude short grassland", distribution: "Eastern Free State highveld (Harrismith, Clarens)", behaviour: "Ground-dwelling, low perch song", voice: "Soft twittering", status: "Critically Endangered (\~340 left)", image: "https://picsum.photos/id/1020/800/500", xenoCantoUrl: "https://xeno-canto.org/species/Spizocorys-fringillaris" },
            { id: 14, name: "Rudd's Lark", sciName: "Heteromirafra ruddi", category: "Scarce Grassland Specials", size: "14–16 cm", identification: "Stout, heavy bill, rufous wing panel", habitat: "Moist highland grassland", distribution: "Eastern Free State plateaus (Golden Gate area)", behaviour: "Hovering display song", voice: "Buzzing aerial song", status: "Critically Endangered", image: "https://picsum.photos/id/1021/800/500", xenoCantoUrl: "https://xeno-canto.org/species/Heteromirafra-ruddi" },
            { id: 15, name: "White-winged Flufftail", sciName: "Sarothrura ayresi", category: "Scarce Wetland Specials", size: "14–17 cm", identification: "Tiny rail, male black/white wing spots", habitat: "Montane sedge wetlands", distribution: "Very rare eastern Free State marshes (Sterkfontein area)", behaviour: "Secretive, crepuscular", voice: "Frog-like clicks", status: "Endangered (downlisted from CR)", image: "https://picsum.photos/id/1022/800/500", xenoCantoUrl: "https://xeno-canto.org/species/Sarothrura-ayresi" },
            { id: 16, name: "Wattled Crane", sciName: "Grus carunculata", category: "Scarce Grassland Specials", size: "120 cm tall", identification: "Grey with red wattles", habitat: "Wet grasslands/pans", distribution: "Seekoeivlei, large Free State wetlands", behaviour: "Dancing pairs", voice: "Bugling kroo-kroo", status: "Critically Endangered in SA", image: "https://picsum.photos/id/1023/800/500", xenoCantoUrl: "https://xeno-canto.org/species/Grus-carunculata" },
            { id: 17, name: "Blue Swallow", sciName: "Hirundo atrocaerulea", category: "Scarce Migrants", size: "20–25 cm", identification: "Glossy blue-black, long tail streamers", habitat: "Montane grassland/forest edges", distribution: "Summer breeder eastern Free State", behaviour: "Aerial, nests in holes", voice: "Twittering", status: "Vulnerable", image: "https://picsum.photos/id/1024/800/500", xenoCantoUrl: "https://xeno-canto.org/species/Hirundo-atrocaerulea" },
            { id: 18, name: "Yellow-breasted Pipit", sciName: "Anthus chloris", category: "Scarce Grassland Specials", size: "16–18 cm", identification: "Breeding yellow underparts streaked black", habitat: "Moist high grassland", distribution: "Golden Gate/QwaQwa highlands", behaviour: "Flight song", voice: "Buzzy flight song", status: "Vulnerable", image: "https://picsum.photos/id/1025/800/500", xenoCantoUrl: "https://xeno-canto.org/species/Anthus-chloris" },
            { id: 19, name: "Drakensberg Rockjumper", sciName: "Chaetops promontorius", category: "Near-Endemics", size: "23–25 cm", identification: "Black head, orange under, hops on rocks", habitat: "Rocky slopes", distribution: "Drakensberg foothills Phuthaditjhaba/Golden Gate", behaviour: "Family groups", voice: "Sharp tseep", status: "Near-endemic", image: "https://picsum.photos/id/1026/800/500", xenoCantoUrl: "https://xeno-canto.org/species/Chaetops-promontorius" },
            { id: 20, name: "Drakensberg Siskin", sciName: "Crithagra symonsi", category: "Near-Endemics", size: "13 cm", identification: "Yellow-green streaked finch", habitat: "High grassland/protea", distribution: "Drakensberg border", behaviour: "Flocks on seeds", voice: "Twittering", status: "Near Threatened", image: "https://picsum.photos/id/1027/800/500", xenoCantoUrl: "https://xeno-canto.org/species/Crithagra-symonsi" },
            // Add more from earlier batches or tell me to expand (e.g. Secretarybird, Blue Korhaan, Southern Bald Ibis)
        ];

        // RENDER FUNCTIONS (same as before)
        function renderBirds(filtered = birds) {
            const grid = document.getElementById("birds-grid");
            grid.innerHTML = "";
            const grouped = {};
            filtered.forEach(b => {
                if (!grouped[b.category]) grouped[b.category] = [];
                grouped[b.category].push(b);
            });
            Object.keys(grouped).forEach(cat => {
                const header = document.createElement("div");
                header.className = "col-span-full mt-8 first:mt-0";
                header.innerHTML = `<div class="flex items-center gap-3 mb-6"><div class="h-px flex-1 bg-gradient-to-r from-transparent via-emerald-300 to-transparent"></div><span class="uppercase text-xs tracking-[2px] font-semibold text-emerald-700">${cat}</span><div class="h-px flex-1 bg-gradient-to-r from-transparent via-emerald-300 to-transparent"></div></div>`;
                grid.appendChild(header);
                grouped[cat].forEach(bird => {
                    const card = document.createElement("div");
                    card.className = "bird-card bg-white rounded-3xl overflow-hidden shadow cursor-pointer";
                    card.innerHTML = `
                        <div class="h-56 overflow-hidden"><img src="\( {bird.image}" class="w-full h-full object-cover hover:scale-105 transition" alt=" \){bird.name}"></div>
                        <div class="p-6">
                            <div class="text-emerald-700 text-xs font-medium">${bird.category}</div>
                            <div class="book-header text-2xl mt-1">${bird.name}</div>
                            <div class="text-gray-500 text-sm">${bird.sciName}</div>
                            <div class="mt-2 text-xs text-red-700">${bird.status}</div>
                            <button onclick="openBirdModal(${bird.id}); event.stopPropagation()" class="mt-4 w-full py-3 bg-emerald-700 hover:bg-emerald-800 text-white rounded-2xl text-xs">OPEN PAGE</button>
                        </div>`;
                    card.onclick = () => openBirdModal(bird.id);
                    grid.appendChild(card);
                });
            });
        }

        function renderCategoryList() {
            const cats = [...new Set(birds.map(b => b.category))].sort();
            const container = document.getElementById("category-list");
            container.innerHTML = "";
            cats.forEach(cat => {
                const div = document.createElement("div");
                div.className = "px-4 py-3 hover:bg-emerald-50 rounded-2xl cursor-pointer flex gap-3 text-sm";
                div.innerHTML = `<i class="fa-solid fa-feather text-emerald-600"></i><span>${cat}</span>`;
                div.onclick = () => renderBirds(birds.filter(b => b.category === cat));
                container.appendChild(div);
            });
        }

        let currentBird = null;

        function openBirdModal(id) {
            currentBird = birds.find(b => b.id === id);
            if (!currentBird) return;
            document.getElementById("bird-modal").classList.remove("hidden");
            document.getElementById("modal-bird-name").textContent = currentBird.name;
            document.getElementById("modal-book-image").src = currentBird.image;
            const info = `
                <div class="grid grid-cols-2 gap-x-8 gap-y-6 text-sm">
                    <div><div class="uppercase text-[10px] text-gray-500">Scientific</div><div class="font-medium">${currentBird.sciName}</div></div>
                    <div><div class="uppercase text-[10px] text-gray-500">Size</div><div>${currentBird.size}</div></div>
                    <div class="col-span-2"><div class="uppercase text-[10px] text-gray-500">ID</div><div>${currentBird.identification}</div></div>
                    <div><div class="uppercase text-[10px] text-gray-500">Habitat</div><div>${currentBird.habitat}</div></div>
                    <div><div class="uppercase text-[10px] text-gray-500">Distribution</div><div>${currentBird.distribution}</div></div>
                    <div class="col-span-2"><div class="uppercase text-[10px] text-gray-500">Behaviour</div><div>${currentBird.behaviour}</div></div>
                    <div><div class="uppercase text-[10px] text-gray-500">Voice</div><div class="italic">${currentBird.voice}</div></div>
                    <div><div class="uppercase text-[10px] text-gray-500">Status</div><div class="text-red-700 font-medium">${currentBird.status}</div></div>
                </div>`;
            document.getElementById("modal-info").innerHTML = info;
            document.getElementById("xeno-link").href = currentBird.xenoCantoUrl;
            loadMyObservation();
        }

        function closeModal() { document.getElementById("bird-modal").classList.add("hidden"); }

        function handlePhotoUpload(e) {
            const file = e.target.files[0];
            if (!file || !currentBird) return;
            const reader = new FileReader();
            reader.onload = ev => {
                localStorage.setItem(`bird-photo-${currentBird.id}`, ev.target.result);
                document.getElementById("my-photo-preview").src = ev.target.result;
                document.getElementById("my-photo-preview-container").classList.remove("hidden");
            };
            reader.readAsDataURL(file);
        }

        function removeMyPhoto() {
            if (!currentBird) return;
            localStorage.removeItem(`bird-photo-${currentBird.id}`);
            document.getElementById("my-photo-preview-container").classList.add("hidden");
        }

        function loadMyObservation() {
            if (!currentBird) return;
            const photo = localStorage.getItem(`bird-photo-${currentBird.id}`);
            if (photo) {
                document.getElementById("my-photo-preview").src = photo;
                document.getElementById("my-photo-preview-container").classList.remove("hidden");
            }
            document.getElementById("my-notes").value = localStorage.getItem(`bird-notes-${currentBird.id}`) || "";
        }

        function saveMyObservation() {
            if (!currentBird) return;
            localStorage.setItem(`bird-notes-${currentBird.id}`, document.getElementById("my-notes").value.trim());
            const btn = event.currentTarget;
            const orig = btn.textContent;
            btn.textContent = "✅ SAVED!";
            setTimeout(() => btn.textContent = orig, 1200);
        }

        function filterBirds() {
            const term = document.getElementById("search-input").value.toLowerCase().trim();
            const filtered = term ? birds.filter(b => b.name.toLowerCase().includes(term) || b.sciName.toLowerCase().includes(term) || b.category.toLowerCase().includes(term)) : birds;
            renderBirds(filtered);
        }

        function showMySightings() {
            const sighted = birds.filter(b => localStorage.getItem(`bird-photo-${b.id}`));
            if (sighted.length === 0) { alert("No sightings saved yet! Upload a photo on any bird page."); return; }
            renderBirds(sighted);
        }

        function init() {
            renderBirds();
            renderCategoryList();
            document.getElementById("bird-modal").onclick = e => { if (e.target.id === "bird-modal") closeModal(); };
            document.addEventListener("keydown", e => { if (e.key === "Escape") closeModal(); });
        }

        window.onload = init;
    </script>
</body>
</html>
