<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gestión de Documentos - COVA</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <style>
        body {
            background-color: #0d1117;
            color: #c9d1d9;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
        }
        .card { background-color: #161b22; border: 1px solid #30363d; }
        .input-field { background-color: #0d1117; border: 1px solid #30363d; color: #c9d1d9; }
        .input-field:focus { border-color: #58a6ff; outline: none; }
        th { background-color: #161b22; color: #8b949e; border-bottom: 2px solid #30363d; }
        td { border-bottom: 1px solid #21262d; }
        @media print {
            body { background-color: white; color: black; padding: 0; }
            .no-print { display: none !important; }
            .print-view { display: block !important; }
            .card { border: none; background: transparent; }
        }
    </style>
</head>
<body class="p-4 md:p-8">

    <div class="no-print max-w-7xl mx-auto">
        <div class="flex flex-col md:flex-row justify-between items-center gap-4 mb-6">
            <h1 class="text-2xl font-bold text-white flex items-center gap-2">
                📑 Generador de Presupuestos, Proformas y Notas
            </h1>
            <a href="index.html" class="bg-gray-800 hover:bg-gray-700 text-gray-300 px-4 py-2 rounded text-sm font-semibold transition">⬅️ Volver a Control de Órdenes</a>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            <div class="lg:col-span-1 card p-6 rounded-xl shadow-lg h-fit">
                <h2 class="text-lg font-bold text-white mb-4 border-b border-gray-800 pb-2">Nuevo Documento</h2>
                <form id="doc-form" onsubmit="guardarDocumento(event)" class="space-y-4">
                    <div>
                        <label class="block text-xs font-bold text-gray-400 mb-1">Tipo de Documento</label>
                        <select id="tipo_doc" class="w-full input-field rounded p-2 text-sm font-bold text-white">
                            <option value="PRESUPUESTO">PRESUPUESTO (PRE)</option>
                            <option value="PROFORMA">PROFORMA (PROF)</option>
                            <option value="NOTA_ENTREGA">NOTA DE ENTREGA (NE)</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-gray-400 mb-1">Empresa Emisora</label>
                        <select id="empresa_emisora" class="w-full input-field rounded p-2 text-sm font-semibold text-white">
                            <option value="Soluciones Integrales 360 F.P">SOLUCIONES 360</option>
                            <option value="Security Mallory 360 C.A">SECURITY MALLORY</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-gray-400 mb-1">Cliente / Empresa Compradora</label>
                        <input type="text" id="cliente_doc" required class="w-full input-field rounded p-2 text-sm uppercase">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-gray-400 mb-1">RIF / Identificación</label>
                        <input type="text" id="rif_doc" placeholder="Ej: J-12345678-9" class="w-full input-field rounded p-2 text-sm">
                    </div>

                    <div class="border-t border-gray-800 pt-3">
                        <label class="block text-xs font-bold text-emerald-400 mb-2">Artículos / Servicios</label>
                        <div id="items-container" class="space-y-2">
                            <div class="flex gap-1 item-row">
                                <input type="number" placeholder="Cant" class="w-16 input-field rounded p-1 text-xs text-center item-cant" required oninput="calcularTotalesForm()">
                                <input type="text" placeholder="Descripción del artículo/servicio" class="flex-1 input-field rounded p-1 text-xs item-desc" required>
                                <input type="number" step="0.01" placeholder="Precio $" class="w-20 input-field rounded p-1 text-xs text-right item-precio" required oninput="calcularTotalesForm()">
                            </div>
                        </div>
                        <button type="button" onclick="agregarFilaItem()" class="mt-2 text-xs text-blue-400 hover:text-blue-300 font-semibold flex items-center gap-1">+ Añadir otra fila</button>
                    </div>

                    <div class="border-t border-gray-800 pt-3 flex justify-between items-center text-sm font-bold text-white">
                        <span>Total Calculado:</span>
                        <span id="form-total-view" class="text-emerald-400 text-lg">$ 0.00</span>
                    </div>

                    <button type="submit" class="w-full bg-blue-600 hover:bg-blue-500 text-white font-bold py-2 px-4 rounded text-sm transition shadow-lg">GENERAR Y ASIGNAR NÚMERO</button>
                </form>
            </div>

            <div class="lg:col-span-2 card p-6 rounded-xl shadow-lg">
                <h2 class="text-lg font-bold text-white mb-4 border-b border-gray-800 pb-2">Historial de Documentos Emitidos</h2>
                <div class="overflow-x-auto">
                    <table class="w-full text-left text-xs whitespace-nowrap">
                        <thead>
                            <tr>
                                <th class="p-3">Numeración</th>
                                <th class="p-3">Tipo</th>
                                <th class="p-3">Emisor</th>
                                <th class="p-3">Cliente</th>
                                <th class="p-3 text-right">Monto Total</th>
                                <th class="p-3 text-center">Acciones</th>
                            </tr>
                        </thead>
                        <tbody id="tabla-documentos">
                            </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <div id="print-area" class="hidden print-view max-w-4xl mx-auto p-6 bg-white text-black text-sm">
        <div class="flex justify-between items-start border-b-2 border-gray-300 pb-4 mb-6">
            <div>
                <h1 id="p-empresa-nombre" class="text-xl font-bold tracking-wide text-gray-900">EMPRESA EMISORA</h1>
                <p class="text-xs text-gray-500 mt-1">Control de Operaciones y Logística Corporativa</p>
                <p id="p-cliente-info" class="text-xs text-gray-700 mt-4 font-semibold"></p>
            </div>
            <div class="text-right">
                <div id="p-badge-tipo" class="bg-gray-900 text-white px-3 py-1 font-bold text-sm rounded mb-2 inline-block">DOCUMENTO</div>
                <p class="text-lg font-mono font-bold text-red-600" id="p-numero">N° 0000</p>
                <p class="text-xs text-gray-500 mt-2" id="p-fecha">Fecha: --/--/----</p>
            </div>
        </div>

        <table class="w-full text-left text-xs mb-6">
            <thead>
                <tr class="border-b border-gray-400 bg-gray-100 font-bold">
                    <th class="p-2 text-black bg-transparent w-16 text-center">CANT.</th>
                    <th class="p-2 text-black bg-transparent">DESCRIPCIÓN</th>
                    <th class="p-2 text-black bg-transparent w-24 text-right">P. UNIT ($)</th>
                    <th class="p-2 text-black bg-transparent w-24 text-right">TOTAL ($)</th>
                </tr>
            </thead>
            <tbody id="p-tabla-cuerpo">
                </tbody>
        </table>

        <div class="flex justify-between items-end mt-12 pt-8 border-t border-gray-200">
            <div class="w-48 text-center border-t border-gray-400 pt-2 text-xs text-gray-500">Recibido por (Cliente)</div>
            <div class="text-right space-y-1">
                <p class="text-base font-bold text-gray-900" id="p-total-final">TOTAL NETO: $ 0.00</p>
                <p class="text-[10px] text-gray-400">Fondos expresados en Divisas ($ USD)</p>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-app.js";
        import { getFirestore, collection, addDoc, getDocs, doc, onSnapshot, query, orderBy } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-firestore.js";

        // Mismas credenciales oficiales
        const firebaseConfig = {
            apiKey: "AIzaSyC_1qzfNPRuU8Fz4quhWIFVfIZGggI7r9A",
            authDomain: "operaciones-e645f.firebaseapp.com",
            projectId: "operaciones-e645f",
            storageBucket: "operaciones-e645f.firebasestorage.app",
            messagingSenderId: "791459219171",
            appId: "1:791459219171:web:93236902494f2cdc63e7d6",
            measurementId: "G-YPK15ZLJ3C"
        };

        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        const docRef = collection(db, "documentos_cova");

        let listaDocumentos = [];

        // Agregar fila de artículo dinámicamente en el formulario
        window.agregarFilaItem = function() {
            const container = document.getElementById('items-container');
            const div = document.createElement('div');
            div.className = "flex gap-1 item-row";
            div.innerHTML = `
                <input type="number" placeholder="Cant" class="w-16 input-field rounded p-1 text-xs text-center item-cant" required oninput="calcularTotalesForm()">
                <input type="text" placeholder="Descripción del artículo/servicio" class="flex-1 input-field rounded p-1 text-xs item-desc" required>
                <input type="number" step="0.01" placeholder="Precio $" class="w-20 input-field rounded p-1 text-xs text-right item-precio" required oninput="calcularTotalesForm()">
            `;
            container.appendChild(div);
        };

        // Calcular total en caliente mientras escribes
        window.calcularTotalesForm = function() {
            let total = 0;
            document.querySelectorAll('.item-row').forEach(row => {
                const cant = parseFloat(row.querySelector('.item-cant').value) || 0;
                const precio = parseFloat(row.querySelector('.item-precio').value) || 0;
                total += (cant * precio);
            });
            document.getElementById('form-total-view').innerText = `$ ${total.toLocaleString('en-US', {minimumFractionDigits: 2})}`;
        };

        // Escuchar base de datos e incrementar numeración correlativa
        onSnapshot(query(docRef, orderBy("creadoEn", "desc")), (snapshot) => {
            listaDocumentos = [];
            snapshot.forEach((snap) => {
                listaDocumentos.push({ id: snap.id, ...snap.data() });
            });
            renderTablaDocs();
        });

        window.guardarDocumento = async function(e) {
            e.preventDefault();
            const tipo = document.getElementById('tipo_doc').value;
            
            // Filtramos cuántos de este tipo existen para calcular el correlativo exacto
            const totalDeEsteTipo = listaDocumentos.filter(d => d.tipo === tipo).length;
            const nuevoNumeroCorrelativo = totalDeEsteTipo + 1;
            
            // Construimos los prefijos limpios solicitados
            let prefijo = "PRE";
            if(tipo === "PROFORMA") prefijo = "PROF";
            if(tipo === "NOTA_ENTREGA") prefijo = "NE";
            const numeroFormateado = `${prefijo}-${nuevoNumeroCorrelativo.toString().padStart(4, '0')}`;

            // Recopilar lista de ítems estructurada
            let items = [];
            let totalGeneral = 0;
            document.querySelectorAll('.item-row').forEach(row => {
                const cant = parseFloat(row.querySelector('.item-cant').value) || 0;
                const desc = row.querySelector('.item-desc').value.toUpperCase();
                const precio = parseFloat(row.querySelector('.item-precio').value) || 0;
                if(cant > 0) {
                    items.push({ cantidad: cant, descripcion: desc, precioUnitario: precio, totalItem: (cant * precio) });
                    totalGeneral += (cant * precio);
                }
            });

            const nuevoDoc = {
                numeroDocumento: numeroFormateado,
                tipo: tipo,
                empresaEmisora: document.getElementById('empresa_emisora').value,
                cliente: document.getElementById('cliente_doc').value.toUpperCase(),
                rif: document.getElementById('rif_doc').value || 'N/A',
                items: items,
                total: totalGeneral,
                fecha: new Date().toLocaleDateString('es-ES'),
                creadoEn: new Date().toISOString()
            };

            await addDoc(docRef, nuevoDoc);
            document.getElementById('doc-form').reset();
            document.getElementById('items-container').innerHTML = `
                <div class="flex gap-1 item-row">
                    <input type="number" placeholder="Cant" class="w-16 input-field rounded p-1 text-xs text-center item-cant" required oninput="calcularTotalesForm()">
                    <input type="text" placeholder="Descripción del artículo/servicio" class="flex-1 input-field rounded p-1 text-xs item-desc" required>
                    <input type="number" step="0.01" placeholder="Precio $" class="w-20 input-field rounded p-1 text-xs text-right item-precio" required oninput="calcularTotalesForm()">
                </div>
            `;
            calcularTotalesForm();
        };

        function renderTablaDocs() {
            const tbody = document.getElementById('tabla-documentos');
            tbody.innerHTML = "";

            listaDocumentos.forEach(d => {
                const tr = document.createElement('tr');
                tr.className = "hover:bg-gray-800/50 transition";
                tr.innerHTML = `
                    <td class="p-3 font-mono font-bold text-blue-400">${d.numeroDocumento}</td>
                    <td class="p-3"><span class="px-2 py-0.5 rounded text-[10px] font-bold ${d.tipo === 'PRESUPUESTO' ? 'bg-blue-950 text-blue-400' : d.tipo === 'PROFORMA' ? 'bg-purple-950 text-purple-400' : 'bg-amber-950 text-amber-400'}">${d.tipo}</span></td>
                    <td class="p-3 text-gray-400 text-[11px]">${d.empresaEmisora.includes("360") ? "SOLUCIONES 360" : "SECURITY MALLORY"}</td>
                    <td class="p-3 font-semibold text-white">${d.cliente}</td>
                    <td class="p-3 text-right text-emerald-400 font-bold">$ ${(d.total || 0).toLocaleString('en-US', {minimumFractionDigits:2})}</td>
                    <td class="p-3 text-center">
                        <button onclick="imprimirDocumentoLocal('${d.id}')" class="bg-gray-700 hover:bg-gray-600 text-white px-3 py-1 rounded font-bold text-[11px] transition">🖨️ Ver / Imprimir PDF</button>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        // Mapear datos al área imprimible profesional y activar comandos de impresión del sistema
        window.imprimirDocumentoLocal = function(id) {
            const d = listaDocumentos.find(doc => doc.id === id);
            if(!d) return;

            document.getElementById('p-empresa-nombre').innerText = d.empresaEmisora.toUpperCase();
            document.getElementById('p-badge-tipo').innerText = d.tipo;
            document.getElementById('p-numero').innerText = `N° ${d.numeroDocumento}`;
            document.getElementById('p-fecha').innerText = `Fecha de Emisión: ${d.fecha}`;
            document.getElementById('p-cliente-info').innerHTML = `CLIENTE: ${d.cliente}<br>RIF/CI: ${d.rif}`;

            const tbodyPrint = document.getElementById('p-tabla-cuerpo');
            tbodyPrint.innerHTML = "";
            
            d.items.forEach(it => {
                const tr = document.createElement('tr');
                tr.className = "border-b border-gray-200 text-black";
                tr.innerHTML = `
                    <td class="p-2 text-center font-bold">${it.cantidad}</td>
                    <td class="p-2">${it.descripcion}</td>
                    <td class="p-2 text-right">$ ${it.precioUnitario.toLocaleString('en-US', {minimumFractionDigits:2})}</td>
                    <td class="p-2 text-right font-semibold">$ ${it.totalItem.toLocaleString('en-US', {minimumFractionDigits:2})}</td>
                `;
                tbodyPrint.appendChild(tr);
            });

            document.getElementById('p-total-final').innerText = `TOTAL NETO: $ ${d.total.toLocaleString('en-US', {minimumFractionDigits:2})}`;
            
            // Disparador de impresión nativa del sistema
            window.print();
        };
    </script>
</body>
</html>
