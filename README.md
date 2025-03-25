# Datos-PPG-para-validacion-en-estimacion-de-glucosa
Este repositorio contiene registros de 23 sujetos donde se puede observar información del tiempo, registros PPG extraídos de la frente, oreja y dedo índice. Su objetivo es proporcionar una herramienta lo cual nos permita extraer archivos .MAT a .CSV y así lograr observar los datos y graficar en MATLAB cada individuo. Con el fin de tener validación para desarrollos relacionados con la estimación de niveles de glucosa en sangre.

Contenido del repositorio:

Datos de tiempo, frente, oreja y dedo índice.

23 archivos de sujetos CSV

Registros de edad, genero, nivel de glucosa en sangre

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
Codigo para graficar en matlab las señales de cada sujeto:

    close all
    clear
    clc

    while true
    % Pedir al usuario el nombre del archivo o salir
    archivo = input('Ingrese el nombre del archivo CSV (escriba "fin" para terminar): ', 's');
    
    % Verificar si el usuario quiere terminar
    if strcmpi(archivo, 'fin')
        disp('Fin del programa');
        break;
    end

    % Intentar cargar el archivo
    try
        data = readtable(archivo);

        % Extraer columnas
        t = data{:, 'Tiempo'}; % Tiempo en segundos
        frente = data{:, 'Frente'}; % Señal de la frente
        oreja = data{:, 'Oreja'}; % Señal de la oreja
        dedo = data{:, 'Dedo_Indice'}; % Señal del dedo índice

        % Graficar cada señal en una figura diferente
        figure;
        plot(t, frente, 'r', 'LineWidth', 1.5);
        xlabel('Tiempo (s)');
        ylabel('Amplitud');
        title(['Señal de la Frente - ' archivo]);
        grid on;

        figure;
        plot(t, oreja, 'b', 'LineWidth', 1.5);
        xlabel('Tiempo (s)');
        ylabel('Amplitud');
        title(['Señal de la Oreja - ' archivo]);
        grid on;

        figure;
        plot(t, dedo, 'g', 'LineWidth', 1.5);
        xlabel('Tiempo (s)');
        ylabel('Amplitud');
        title(['Señal del Dedo Índice - ' archivo]);
        grid on;
    end
