<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Sikkim Monasteries Interactive Map</title>
  <style>
    body {margin:0; background:#f8f6ef; overflow:hidden;}
    h2 {text-align:center; margin:15px 0 8px 0;}
    #map-container {
      width: 100vw; height: 90vh;
      overflow: hidden;
      position: relative;
      background: #f8f6ef;
      border: 2px solid #ccc;
    }
    #map-wrapper {
      position: absolute;
      top: 0; left: 0;
      transform-origin: top left;
      cursor: grab;
    }
    #map-wrapper:active { cursor: grabbing; }
    #base-map { width: 100%; display:block; }

    .marker {
      position: absolute;
      width: 28px; height: 28px;
      background: rgba(220,40,40,0.85);
      border-radius: 50%;
      border: 2px solid #fff;
      cursor: pointer;
      transform: translate(-50%, -50%);
      box-shadow: 0 2px 5px rgba(0,0,0,0.2);
      transition: background .2s;
    }
    .marker:hover { background: gold; }

    .info-popup {
      position: absolute; 
      background: #fffbe7;
      border: 1px solid #b4460f; 
      border-radius: 8px;
      box-shadow: 0 2px 12px rgba(0,0,0,0.15);
      padding: 10px 14px; 
      min-width:200px;
      font-family: 'Segoe UI',sans-serif; 
      font-size:15px;
      display: none;
      z-index: 99;
    }
    .info-popup .close-btn {
      position: absolute;
      top: 6px; right: 6px;
      cursor:pointer;
      background: #fde7c1; 
      border-radius:50%; 
      width:22px; 
      height:22px; 
      text-align:center; 
      font-size:16px; 
      border:none;
      line-height:20px;
    }

    @media (max-width: 700px) {
      .info-popup { font-size: 13px; min-width:140px; }
      .marker { width:22px;height:22px; }
    }
  </style>
</head>
<body>
<h2>Digitize & Showcase Sikkim Monasteries – Interactive Map</h2>

<div id="map-container">
  <div id="map-wrapper">
    <img id="base-map" src="sikkim_road_political_map.png" alt="Sikkim Political and Road Map">
  </div>
</div>

<script>
  const markers = [
    { name: "Rumtek Monastery", desc: "East Sikkim | Kagyu | Largest monastery, seat of 16th Karmapa.<br>Activities: Prayer wheel, meditation, architecture photography.", percentX: 74, percentY: 52 },
    { name: "Pemayangtse Monastery", desc: "West Sikkim | Nyingma | 1705, sublime lotus.<br>Activities: Wooden masterpieces, Kanchenjunga view, heritage photography.", percentX: 27, percentY: 53 },
    { name: "Tashiding Monastery", desc: "West Sikkim | Nyingma | 1651, holiest site.<br>Activities: Bumchu festival, sacred vase, river valley trekking.", percentX: 40, percentY: 62 },
    { name: "Phodong Monastery", desc: "North Sikkim | Kagyu | Colorful murals.<br>Activities: Architecture appreciation, local crafts.", percentX: 72, percentY: 30 },
    { name: "Dubdi Monastery", desc: "West Sikkim | Nyingma | 1701, oldest.<br>Activities: Trekking, bird watching, history immersion.", percentX: 36, percentY: 43 },
    { name: "Enchey Monastery", desc: "East Sikkim | Nyingma | Peaceful temple.<br>Activities: Pine walks, Cham festival, cultural programs.", percentX: 84, percentY: 55 }
  ];

  const wrapper = document.getElementById('map-wrapper');
  const container = document.getElementById('map-container');

  // Create markers
  markers.forEach(mark => {
    const el = document.createElement('div');
    el.className = 'marker';
    el.title = mark.name;
    el.style.left = `${mark.percentX}%`;
    el.style.top = `${mark.percentY}%`;

    const pop = document.createElement('div');
    pop.className = 'info-popup';
    pop.innerHTML = `<button class="close-btn">&times;</button>
                     <strong>${mark.name}</strong><br>${mark.desc}`;
    wrapper.appendChild(el);
    wrapper.appendChild(pop);

    function showPopup() {
      closeAllPopups();
      pop.style.left = (mark.percentX + 2) + '%';
      pop.style.top = (mark.percentY - 2) + '%';
      pop.style.display = 'block';
    }

    el.onclick = (e) => { showPopup(); e.stopPropagation(); };
    pop.querySelector('.close-btn').onclick = (e) => { pop.style.display = 'none'; e.stopPropagation(); };
  });

  function closeAllPopups() {
    document.querySelectorAll('.info-popup').forEach(p => p.style.display = 'none');
  }
  document.body.addEventListener('click', closeAllPopups);

  // ---- Zoom + Pan Logic ----
  let scale = 1, posX = 0, posY = 0;
  let isPanning = false, startX, startY;

  function updateTransform() {
    wrapper.style.transform = `translate(${posX}px, ${posY}px) scale(${scale})`;
  }

  // Zoom with mouse wheel
  container.addEventListener('wheel', e => {
    e.preventDefault();
    const delta = e.deltaY > 0 ? 0.9 : 1.1; 
    scale *= delta;
    scale = Math.min(Math.max(0.5, scale), 4); // limit zoom
    updateTransform();
  });

  // Pan with mouse drag
  container.addEventListener('mousedown', e => {
    isPanning = true;
    startX = e.clientX - posX;
    startY = e.clientY - posY;
  });
  container.addEventListener('mousemove', e => {
    if (!isPanning) return;
    posX = e.clientX - startX;
    posY = e.clientY - startY;
    updateTransform();
  });
  container.addEventListener('mouseup', () => isPanning = false);
  container.addEventListener('mouseleave', () => isPanning = false);

  // Touch support (mobile pinch zoom could be added later)
</script>
</body>
</html>
