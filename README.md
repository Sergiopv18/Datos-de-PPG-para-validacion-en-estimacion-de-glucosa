Este repositorio contiene registros de 23 sujetos donde se puede observar información del tiempo, registros PPG extraídos de la frente, oreja y dedo índice. Su objetivo es proporcionar una herramienta lo cual nos permita extraer archivos .MAT a .CSV y así lograr observar los datos y graficar en MATLAB cada individuo. Con el fin de tener validación para desarrollos relacionados con la estimación de niveles de glucosa en sangre.

Contenido del repositorio:

Datos de tiempo, frente, oreja y dedo índice.

Formato: 23 archivos de sujetos CSV

Código para extraer los archivos .mat a .csv:

    % Definir carpetas de entrada y salida
    carpeta_mat = 'archivos_mat'; 
    carpeta_csv = 'guardar_csv';  

    % Crear carpeta de salida si no existe
    if ~exist(carpeta_csv, 'dir')
    mkdir(carpeta_csv);
    end

    % Obtener archivos .MAT en la carpeta
    archivos = dir(fullfile(carpeta_mat, '*.mat')); % Buscar archivos con extensión .mat


    % Procesar cada archivo .MAT
    for k = 1:length(archivos)
    archivo_mat = fullfile(carpeta_mat, archivos(k).name);
    fprintf('Procesando archivo: %s\n', archivos(k).name);
    
    % Cargar datos
    datos = load(archivo_mat);
    
    % Verificar si las variables existen
    if isfield(datos, 't') && isfield(datos, 'y') && isfield(datos, 'y1') && isfield(datos, 'y2')
        t = datos.t(:);
        y = datos.y(:);
        y1 = datos.y1(:);
        y2 = datos.y2(:);

        % Convertir matriz a tabla con encabezados
        tabla_csv = table(t, y, y1, y2, 'VariableNames', {'Tiempo', 'Frente', 'Oreja', 'Dedo_Indice'});

        % Reemplazar extensión .MAT o .mat por .csv
        [~, nombre_base, ~] = fileparts(archivos(k).name);
        nombre_csv = strcat(nombre_base, '.csv');
        ruta_csv = fullfile(carpeta_csv, nombre_csv);

        % Guardar en CSV con encabezados
        writetable(tabla_csv, ruta_csv);

        fprintf('Archivo guardado: %s\n', ruta_csv);
    else
        fprintf('El archivo %s no contiene las variables requeridas.\n', archivos(k).name);
    end

