<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>FormalJob — Prototipo AI para Formalización y Empleabilidad</title>
  <meta name="description" content="Plataforma móvil de bajo consumo de datos con capacitación y emparejamiento IA para trabajadores informales." />
  <style>
    /* Mobile-first, ligera y sin imágenes por defecto (modo bajo datos) */
    :root{
      --primary:#0066cc;
      --primary-dark:#004c99;
      --bg:#f7f9fb;
      --card:#ffffff;
      --muted:#6b7280;
      --radius:10px;
      font-family:system-ui,-apple-system,Segoe UI,Roboto,"Helvetica Neue",Arial;
    }
    *{box-sizing:border-box}
    html,body{height:100%;margin:0;background:var(--bg);color:#0f1724}
    header{
      background:var(--primary);
      color:white;
      padding:14px 12px;
      display:flex;
      gap:12px;
      align-items:center;
      justify-content:space-between;
    }
    header h1{font-size:18px;margin:0}
    header p{font-size:12px;margin:0;opacity:.95}
    main{padding:12px;max-width:920px;margin:12px auto}
    .card{background:var(--card);padding:12px;border-radius:var(--radius);box-shadow:0 1px 4px rgba(2,6,23,.06);margin-bottom:12px}
    label{font-size:13px;color:var(--muted);display:block;margin-top:8px}
    input[type=text],input[type=number],select,textarea{
      width:100%;padding:10px;margin-top:6px;border:1px solid #e6e9ef;border-radius:8px;font-size:14px;
      background:transparent;
    }
    button.btn{
      display:inline-block;background:var(--primary);color:white;padding:10px 12px;border:none;border-radius:8px;
      cursor:pointer;font-weight:600;font-size:14px;margin-top:10px;
    }
    button.ghost{background:transparent;border:1px solid #d1d5db;color:var(--muted)}
    .row{display:flex;gap:8px}
    .col{flex:1}
    .small{font-size:13px;color:var(--muted)}
    .section-title{display:flex;align-items:center;justify-content:space-between;margin-bottom:8px}
    .list{display:grid;gap:8px}
    .course, .vacancy{border-left:4px solid var(--primary);padding:10px;border-radius:6px;background:linear-gradient(180deg, rgba(0,0,0,0.01), transparent)}
    footer{font-size:12px;color:var(--muted);text-align:center;padding:8px}

    /* Modal (simple, lightweight) */
    .modal{position:fixed;inset:0;background:rgba(2,6,23,.45);display:none;align-items:center;justify-content:center;padding:12px}
    .modal.open{display:flex}
    .modal .dialog{max-width:760px;width:100%;background:var(--card);padding:12px;border-radius:12px}

    /* Responsive */
    @media(min-width:720px){
      header h1{font-size:20px}
      .row.desktop{display:flex}
      .col-2{flex:0 0 48%}
    }
    /* Low-data indicator */
    .low-data-badge{background:#fff8e1;border:1px solid #ffecb3;padding:6px;border-radius:8px;font-size:12px;color:#7a4d00}
  </style>
</head>
<body>
  <header role="banner">
    <div>
      <h1>FormalJob — Prototipo AI</h1>
      <p>Capacitación + Emparejamiento IA para reducir la informalidad</p>
    </div>
    <div style="text-align:right">
      <div class="small low-data-badge" id="lowDataBadge" title="Modo bajo consumo">Modo bajo datos: activado</div>
      <button id="toggleLowData" class="btn" style="margin-left:8px;margin-top:8px;font-size:12px">Cambiar modo</button>
    </div>
  </header>

  <main role="main" id="app">
    <!-- Perfil de trabajador -->
    <section class="card" aria-labelledby="perfilTitle">
      <div class="section-title">
        <h2 id="perfilTitle" style="margin:0;font-size:16px">Registro del Trabajador</h2>
        <div class="small">Almacena localmente (offline-friendly)</div>
      </div>

      <form id="profileForm" onsubmit="return false;" aria-describedby="profileDesc">
        <div id="profileDesc" class="small">Llena tu perfil para recibir recomendaciones personalizadas.</div>

        <label for="name">Nombre completo</label>
        <input id="name" type="text" placeholder="Ej: Juana Pérez" required />

        <label for="trade">Oficio / Rol</label>
        <input id="trade" type="text" placeholder="Ej: vendedor ambulante, carpintero" required />

        <div class="row">
          <div class="col">
            <label for="experience">Años de experiencia</label>
            <input id="experience" type="number" min="0" placeholder="Ej: 2" />
          </div>
          <div class="col">
            <label for="location">Ubicación</label>
            <input id="location" type="text" placeholder="Ciudad / Barrio" />
          </div>
        </div>

        <label for="skills">Principales habilidades (separadas por comas)</label>
        <input id="skills" type="text" placeholder="Ej: atención al cliente, cocina, ventas, electricidad" />

        <div style="display:flex;gap:8px;flex-wrap:wrap;margin-top:10px">
          <button class="btn" id="saveProfile">Guardar perfil</button>
          <button class="ghost" id="clearProfile" type="button">Borrar perfil</button>
          <button class="ghost" id="simulateProfile" type="button">Cargar ejemplo</button>
        </div>

        <div id="profileSaved" class="small" style="margin-top:8px;display:none;color:green">Perfil guardado localmente.</div>
      </form>
    </section>

    <!-- Módulo de capacitación -->
    <section class="card" aria-labelledby="capTitle">
      <div class="section-title">
        <h2 id="capTitle" style="margin:0;font-size:16px">Módulo de Capacitación Digital</h2>
        <div class="small">Cursos cortos y prácticos, listos para móvil</div>
      </div>

      <div class="small">Consejos: cursos offline-packables, videos comprimidos y material en texto para bajo consumo de datos.</div>

      <div style="margin-top:10px" id="coursesList" class="list" aria-live="polite">
        <!-- Cursos generados por JS -->
      </div>
    </section>

    <!-- Módulo de empleabilidad con IA -->
    <section class="card" aria-labelledby="empTitle">
      <div class="section-title">
        <h2 id="empTitle" style="margin:0;font-size:16px">Módulo de Empleabilidad con IA (Simulado)</h2>
        <div class="small">Emparejamiento entre tu perfil y vacantes usando análisis de palabras clave</div>
      </div>

      <div class="row" style="margin-top:8px">
        <button class="btn col" id="matchBtn">Generar recomendaciones IA</button>
        <button class="ghost col" id="viewVacancies">Ver vacantes</button>
      </div>

      <div id="recoArea" style="margin-top:10px"></div>
    </section>

    <!-- Formalización -->
    <section class="card" aria-labelledby="formalTitle">
      <div class="section-title">
        <h2 id="formalTitle" style="margin:0;font-size:16px">Apoyo para Formalizar un Negocio</h2>
        <div class="small">Guía paso a paso, enlaces y checklist</div>
      </div>

      <p class="small">Esta guía es interactiva y está pensada para consumo de datos reducido (texto, formularios breves).</p>
      <div style="display:flex;gap:8px;flex-wrap:wrap">
        <button class="btn" id="startFormal">Iniciar proceso de formalización</button>
        <button class="ghost" id="downloadChecklist">Descargar checklist (texto)</button>
      </div>
    </section>

    <!-- ODS y fundamentación breve -->
    <section class="card" aria-labelledby="odsTitle">
      <h3 id="odsTitle" style="margin:0 0 8px 0">Impacto y fundamentación</h3>
      <p class="small" style="margin:0 0 6px 0">
        Solución desde ingeniería de sistemas: integra interfaz ligera, bases de datos locales y algoritmos de
        recomendación (IA) para reducir la brecha laboral. Contribuye a:
      </p>
      <ul class="small">
        <li>ODS 8 — Trabajo decente y crecimiento económico</li>
        <li>ODS 10 — Reducción de desigualdades</li>
      </ul>
      <div class="small">Sugerencia técnica: desplegar API ligera (Node/Flask) + motor de recomendaciones (embeddings + vector DB) para producción.</div>
    </section>

    <footer class="card small">
      Prototipo — diseñado para dispositivos de bajo consumo de datos. Datos demo guardados localmente en el navegador.
    </footer>
  </main>

  <!-- Modal simple -->
  <div id="modal" class="modal" role="dialog" aria-modal="true" aria-hidden="true">
    <div class="dialog" role="document">
      <div id="modalContent"></div>
      <div style="text-align:right;margin-top:12px">
        <button class="btn" id="closeModal">Cerrar</button>
      </div>
    </div>
  </div>

  <script>
    // ===== Datos de ejemplo (simulación local, sin llamadas externas) =====
    const COURSES = [
      { id: 'c1', title: 'Manipulación de alimentos', tags: ['cocina','alimentación','salud'], duration:'6 horas', mode:'Texto + video comprimido' },
      { id: 'c2', title: 'Servicio al cliente básico', tags:['ventas','atención al cliente'], duration:'4 horas', mode:'Texto' },
      { id: 'c3', title: 'Emprendimiento y registro', tags:['emprendimiento','legal'], duration:'3 horas', mode:'Texto' },
      { id: 'c4', title: 'Inglés técnico básico', tags:['inglés','comunicacion'], duration:'8 horas', mode:'Audio comprimido' },
      { id: 'c5', title: 'Mantenimiento básico de herramientas', tags:['mecánica','electricidad'], duration:'5 horas', mode:'Texto' }
    ];

    const VACANCIES = [
      { id:'v1', title:'Auxiliar de cocina', requirements:['cocina','manipulación','atención al cliente'] },
      { id:'v2', title:'Vendedor punto fijo', requirements:['ventas','atención al cliente','registro'] },
      { id:'v3', title:'Ayudante de mantenimiento', requirements:['mecánica','herramientas','electricidad'] },
      { id:'v4', title:'Auxiliar administrativo (jornada parcial)', requirements:['registro','digital','ventas'] },
    ];

    // ===== Manejo de perfil local (localStorage) =====
    const qs = id => document.getElementById(id);
    const profileFormElements = ['name','trade','experience','location','skills'].map(qs);
    const saveProfileBtn = qs('saveProfile');
    const clearProfileBtn = qs('clearProfile');
    const simulateProfileBtn = qs('simulateProfile');
    const profileSavedEl = qs('profileSaved');

    function getProfile(){
      const raw = localStorage.getItem('formaljob_profile');
      return raw ? JSON.parse(raw) : null;
    }
    function setProfile(profile){
      localStorage.setItem('formaljob_profile', JSON.stringify(profile));
    }
    function clearProfile(){
      localStorage.removeItem('formaljob_profile');
    }
    function loadProfileToForm(){
      const p = getProfile();
      if(!p) return;
      qs('name').value = p.name||'';
      qs('trade').value = p.trade||'';
      qs('experience').value = p.experience||'';
      qs('location').value = p.location||'';
      qs('skills').value = (p.skills||[]).join(', ');
    }

    saveProfileBtn.addEventListener('click', e=>{
      const profile = {
        name: qs('name').value.trim(),
        trade: qs('trade').value.trim(),
        experience: qs('experience').value,
        location: qs('location').value.trim(),
        skills: qs('skills').value.split(',').map(s=>s.trim().toLowerCase()).filter(Boolean)
      };
      if(!profile.name || !profile.trade) {
        alert('Por favor completa tu nombre y oficio.');
        return;
      }
      setProfile(profile);
      profileSavedEl.style.display = 'block';
      setTimeout(()=>profileSavedEl.style.display='none',3000);
    });

    clearProfileBtn.addEventListener('click', ()=>{
      if(confirm('Borrar perfil guardado localmente?')) {
        clearProfile();
        profileFormElements.forEach(el=>el.value='');
      }
    });

    simulateProfileBtn.addEventListener('click', ()=>{
      const demo = {
        name:'Luis Gómez',
        trade:'Vendedor ambulante',
        experience:2,
        location:'Cali - Oeste',
        skills:['ventas','atención al cliente','registro']
      };
      setProfile(demo);
      loadProfileToForm();
      profileSavedEl.style.display = 'block';
      setTimeout(()=>profileSavedEl.style.display='none',3000);
    });

    // ===== Mostrar cursos (modo bajo datos -> no imágenes, texto comprimido) =====
    const coursesList = qs('coursesList');
    function renderCourses(){
      coursesList.innerHTML = '';
      COURSES.forEach(c=>{
        const el = document.createElement('div');
        el.className = 'course';
        el.innerHTML = `<strong>${c.title}</strong> <div class="small">${c.duration} · ${c.mode}</div>
                        <div class="small" style="margin-top:6px">Etiquetas: ${c.tags.join(', ')}</div>
                        <div style="margin-top:8px"><button class="ghost" data-id="${c.id}">Ver contenido (simulado)</button></div>`;
        coursesList.appendChild(el);
      });

      // Delegación para botones ver contenido
      coursesList.querySelectorAll('button').forEach(btn=>{
        btn.addEventListener('click', e=>{
          const id = btn.dataset.id;
          openModal(`<h3>${COURSES.find(x=>x.id===id).title}</h3>
            <p class="small">Contenido demo: texto con actividades y enlaces de bajo consumo. (Aquí se integraría SCORM o paquetes descargables).</p>`);
        });
      });
    }

    // ===== Emparejamiento IA (simulación local, algoritmo simple de palabras clave) =====
    const matchBtn = qs('matchBtn');
    const recoArea = qs('recoArea');

    function scoreMatch(profile, vacancy){
      // Score = cantidad de requerimientos presentes en habilidades + trade
      const pSkills = (profile.skills||[]).map(s=>s.toLowerCase());
      let score = 0;
      vacancy.requirements.forEach(r=>{
        const rr = r.toLowerCase();
        if(pSkills.includes(rr)) score++;
        if(profile.trade && profile.trade.toLowerCase().includes(rr)) score++;
      });
      return score;
    }

    function recommend(profile){
      if(!profile) return {error:'No hay perfil guardado. Guarda tu perfil para obtener recomendaciones.'};
      // Vacantes ordenadas por score
      const vacs = VACANCIES.map(v=>{
        return {...v, score: scoreMatch(profile,v)};
      }).sort((a,b)=>b.score-a.score);

      // Cursos recomendados: los que complementan habilidades faltantes según top vacancy
      const top = vacs[0];
      const missing = (top ? top.requirements.filter(r=>{
        const pSkills = (profile.skills||[]).map(s=>s.toLowerCase());
        return !pSkills.includes(r.toLowerCase()) && !(profile.trade||'').toLowerCase().includes(r.toLowerCase());
      }) : []);
      // Select courses that cover missing tags
      const recommendedCourses = COURSES.filter(c=>{
        return c.tags.some(t=>missing.includes(t));
      });
      // If none, suggest upskilling generic
      return {vacs: vacs.slice(0,4), recommendedCourses: recommendedCourses.length ? recommendedCourses : COURSES.slice(0,2), missing};
    }

    matchBtn.addEventListener('click', ()=>{
      const profile = getProfile();
      const res = recommend(profile);
      if(res.error){
        recoArea.innerHTML = `<div class="small" style="color:#b91c1c">${res.error}</div>`;
        return;
      }
      // Render recommendations
      let html = `<div class="small">Vacantes sugeridas (ordenadas por afinidad)</div><div style="margin-top:8px">`;
      res.vacs.forEach(v=>{
        html += `<div class="vacancy" style="margin-bottom:6px">
          <strong>${v.title}</strong>
          <div class="small">Afinidad: ${v.score} · Requisitos: ${v.requirements.join(', ')}</div>
          <div style="margin-top:6px"><button class="ghost" data-vac="${v.id}">Ver detalle / Postular (simulado)</button></div>
        </div>`;
      });
      html += '</div>';

      html += `<div style="margin-top:10px" class="small"><strong>Cursos recomendados para mejorar afinidad:</strong></div><div style="margin-top:8px">`;
      res.recommendedCourses.forEach(c=>{
        html += `<div class="course" style="margin-bottom:6px"><strong>${c.title}</strong><div class="small">${c.duration} · ${c.mode}</div></div>`;
      });
      html += '</div>';

      recoArea.innerHTML = html;

      // Attach handlers for vacancy buttons
      recoArea.querySelectorAll('button[data-vac]').forEach(b=>{
        b.addEventListener('click', e=>{
          const id = b.dataset.vac;
          const v = VACANCIES.find(x=>x.id===id);
          openModal(`<h3>${v.title}</h3><p class="small">Requisitos: ${v.requirements.join(', ')}</p>
            <p class="small">Simulación de postulación: se enviará tu perfil guardado a la vacante (demo local).</p>
            <pre class="small" style="background:#f3f4f6;padding:8px;border-radius:6px;">${JSON.stringify(getProfile(),null,2)}</pre>`);
        });
      });
    });

    // ===== Modal helpers =====
    const modal = qs('modal');
    const modalContent = qs('modalContent');
    const closeModal = qs('closeModal');
    function openModal(html){
      modalContent.innerHTML = html;
      modal.classList.add('open');
      modal.setAttribute('aria-hidden','false');
    }
    function hideModal(){
      modal.classList.remove('open');
      modal.setAttribute('aria-hidden','true');
    }
    closeModal.addEventListener('click', hideModal);
    modal.addEventListener('click', (e)=>{ if(e.target===modal) hideModal(); });

    // ===== Formalización (guía paso a paso simple) =====
    qs('startFormal').addEventListener('click', ()=>{
      openModal(`<h3>Proceso de formalización (resumen)</h3>
        <ol class="small">
          <li>Revisar requisitos para RUT — recopilar documentos (ID, domicilio).</li>
          <li>Crear cuenta DIAN / seleccionar régimen (si aplica).</li>
          <li>Registro mercantil / Cámara de comercio (si aplica).</li>
          <li>Afiliación a seguridad social (Salud y Pensiones) — opciones subsidiadas según ingreso.</li>
          <li>Asesoría para facturación y cumplimiento fiscal.</li>
        </ol>
        <p class="small">Este proceso debe complementarse con asistencia local presencial o remota. Podemos integrar un flujo de acompañamiento con gestores en la siguiente fase.</p>`);
    });

    qs('downloadChecklist').addEventListener('click', ()=>{
      // Generar archivo de texto simple (bajo datos)
      const text = `Checklist de formalización:
- Documento de identidad
- Comprobante de domicilio
- Datos del negocio (nombre, actividad)
- Registro RUT (DIAN)
- Afiliación salud/pensiones
- Registro mercantil (si aplica)
- Plan de negocios / costos
`;
      const blob = new Blob([text],{type:'text/plain'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = 'checklist_formalizacion.txt';
      document.body.appendChild(a);
      a.click();
      a.remove();
      URL.revokeObjectURL(url);
    });

    // ===== Modo bajo datos (UX + accesibilidad) =====
    const lowDataKey = 'formaljob_lowdata';
    const lowDataBadge = qs('lowDataBadge');
    const toggleLowData = qs('toggleLowData');

    function isLowData(){ return localStorage.getItem(lowDataKey) !== 'false'; }
    function updateLowDataUI(){
      const on = isLowData();
      lowDataBadge.textContent = 'Modo bajo datos: ' + (on ? 'activado' : 'desactivado');
      // If we had images/animations they'd be disabled here.
      toggleLowData.textContent = on ? 'Desactivar modo' : 'Activar modo';
    }
    toggleLowData.addEventListener('click', ()=>{
      const toggle = !isLowData();
      localStorage.setItem(lowDataKey, toggle ? 'true' : 'false');
      updateLowDataUI();
      // Small announcement for screen readers
      alert('Modo bajo datos ' + (toggle ? 'activado' : 'desactivado') + '.');
    });

    // ===== Inicialización =====
    function init(){
      // Default low-data true for target audience
      if(localStorage.getItem(lowDataKey) === null) localStorage.setItem(lowDataKey,'true');
      updateLowDataUI();
      renderCourses();
      loadProfileToForm();
    }
    init();

    // ===== Mejora posible (documentación inline) =====
    // Siguientes integraciones recomendadas:
    // 1) Backend mínimo (Node/Flask) que reciba perfiles y devuelva recomendaciones generadas por:
    //    - Motor de embeddings (OpenAI/FAISS/Weaviate) para similaridad entre perfil y vacantes
    //    - Modelo de ranking personalizado (LightGBM o re-ranker)
    // 2) Paquetes SCORM o contenidos offline (zip + manifest) para el módulo de capacitación
    // 3) Autenticación ligera (OTP / social) y encriptado de datos sensibles en tránsito
    // 4) Métricas y telemetría (opt-in) para medir impacto en ODS (empleabilidad y reducción de informalidad)
  </script>
</body>
</html>
