<!-- Vista Imprimible de Factura/Documento (Se activa al imprimir) -->
    <div id="print-area" class="hidden print-view max-w-4xl mx-auto p-6 bg-white text-black text-sm">
        
        <!-- Título Dinámico Gigante -->
        <div class="mb-6">
            <h1 id="p-documento-titulo-grande" class="text-4xl font-extrabold tracking-tight text-gray-800 uppercase">PRESUPUESTO</h1>
        </div>

        <!-- Encabezado corporativo -->
        <div class="flex justify-between items-start border-b-2 border-gray-300 pb-4 mb-6">
            <div>
                <h2 id="p-empresa-nombre" class="text-xl font-bold tracking-wide text-gray-900">EMPRESA EMISORA</h2>
                <p class="text-xs text-gray-500 mt-1">Control de Operaciones y Logística Corporativa</p>
                <p id="p-cliente-info" class="text-xs text-gray-700 mt-4 font-semibold"></p>
            </div>
            <div class="text-right">
                <div id="p-badge-tipo" class="bg-gray-900 text-white px-3 py-1 font-bold text-sm rounded mb-2 inline-block">PRESUPUESTO</div>
                <p class="text-lg font-mono font-bold text-red-600" id="p-numero">N° 0000</p>
                <p class="text-xs text-gray-500 mt-2" id="p-fecha">Fecha: --/--/----</p>
            </div>
        </div>
    </script>
</body>
</html>
