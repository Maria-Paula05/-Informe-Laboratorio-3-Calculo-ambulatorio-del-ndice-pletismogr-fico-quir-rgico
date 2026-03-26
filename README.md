# Informe Laboratorio 3 : Calculo ambulatorio del índice pletismográfico quirúrgico

# Integrantes

-María Paula Fernández Jiménez
-Jhonathan David Guevara Ramírez
-Juan Pablo Díaz Rocha

# 1. Introducción

La monitorización de la nocicepción durante procedimientos quirúrgicos y en contextos experimentales es un tema de gran interés en ingeniería biomédica y anestesiología, ya que permite estimar de forma objetiva la respuesta fisiológica del organismo frente a estímulos nocivos. En la guía de laboratorio, esta práctica se enfoca en el uso de la señal fotopletismográfica (PPG) para estimar el índice pletismográfico quirúrgico (SPI), un parámetro que busca reflejar el balance entre nocicepción y antinocicepción a partir de cambios en la amplitud de la onda pletismográfica y en variables cardiovasculares. La guía señala que el SPI varía entre 0 y 100 y que, durante anestesia general, suelen buscarse valores aproximados entre 20 y 50.

La fotopletismografía es una técnica óptica no invasiva ampliamente utilizada para detectar cambios en el volumen sanguíneo periférico. Su importancia radica en que permite obtener información hemodinámica de manera continua, sencilla y de bajo costo, por lo que se ha convertido en una herramienta útil tanto en aplicaciones clínicas como en sistemas biomédicos portátiles. Además de emplearse en pulsioximetría y monitoreo cardiovascular, la señal PPG también ha sido estudiada como fuente de información para estimar actividad autonómica, perfusión y respuesta fisiológica al estrés.

En la versión original de la guía, la adquisición de la señal PPG se plantea mediante un circuito analógico construido con sensor de reflectancia, amplificador operacional y etapas de acondicionamiento. Sin embargo, para el desarrollo de este laboratorio no se utilizará ese circuito, sino un sensor PPG MAX30102, que integra LEDs, fotodetector, elementos ópticos y electrónica de bajo ruido con rechazo de luz ambiental. Esta decisión permite simplificar la adquisición, mejorar la estabilidad de la señal y centrar el análisis en el procesamiento digital y en el cálculo del SPI, sin apartarse del objetivo principal de la práctica, que sigue siendo la obtención de la señal pletismográfica para la estimación de un índice fisiológico asociado a nocicepción.

Adicionalmente, la práctica propone validar el comportamiento del SPI mediante una maniobra tipo Cold Pressor Test, la cual consiste en exponer al voluntario a un estímulo frío para inducir una respuesta autonómica. Este tipo de prueba ha sido ampliamente utilizado para estudiar la activación simpática y los cambios cardiovasculares frente al estrés fisiológico, por lo que resulta adecuada para observar variaciones en señales derivadas de la PPG. En este contexto, el laboratorio no busca reemplazar la evaluación clínica del dolor, sino explorar cómo una señal óptica periférica puede servir como base para construir indicadores fisiológicos útiles en monitoreo biomédico.

Aunque la guía propone la implementación de un circuito analógico de adquisición PPG, en este trabajo se reemplaza dicha etapa por el uso del sensor integrado MAX30102, manteniendo el mismo propósito de capturar la señal fotopletismográfica necesaria para el cálculo del SPI.

# 2. Objetivos

# 2.1 Objetivo general

Desarrollar un sistema de medición continua del índice pletismográfico quirúrgico (SPI) en condiciones ambulatorias, utilizando la señal PPG adquirida mediante un sensor MAX30102.
La guía establece como objetivo general desarrollar un sistema de medición continua del SPI en condiciones ambulatorias.

# 2.2 Objetivos específicos

-Reconocer las características fundamentales de la onda de pulso a partir de las cuales se obtiene el SPI.

-Implementar un sistema de adquisición de señal PPG usando el sensor MAX30102 en lugar del circuito analógico propuesto en la guía.

-Desarrollar un algoritmo en MATLAB capaz de capturar la señal, detectar máximos y mínimos y calcular el SPI en tiempo real.

-Evaluar el comportamiento del SPI en reposo y durante una maniobra fisiológica tipo Cold Pressor Test.

-Analizar el alcance y las limitaciones del sistema implementado para la estimación indirecta de nocicepción.


# 3. Descripción general de la práctica

La guía plantea el diseño y elaboración de un sistema capaz de suministrar continuamente el SPI, contextualizando al estudiante sobre la forma en que este índice se obtiene en la práctica clínica. En el presente desarrollo, el principio fisiológico permanece igual, ya que se usa la onda de pulso periférica; la diferencia principal radica en la etapa de adquisición, que en vez de implementarse con el circuito analógico mostrado en la guía, se resuelve por medio del sensor óptico MAX30102, el cual integra fuente emisora, fotodetector y electrónica básica de captura.

# 4. Materiales 

# 4.1 Materiales del laboratorio

-Computador con acceso a Internet

-MATLAB

-Arduino UNO o LEONARDO

-Cables de conexión(Jumpers)

-Sensor PPG MAX30102

-Fuente de alimentación USB de 5 V

-Hielo

# 4.2 Aclaración sobre la modificación del montaje

En la guía original se solicita construir un circuito en proto-board con resistencias, condensadores, transistor 2N3904, sensor de reflectancia TCRT1000 o TCST110 y amplificador operacional LM358. En este informe no se utilizará ese circuito, sino un sensor MAX30102 para adquirir directamente la señal PPG. Esta adaptación reemplaza específicamente la etapa de sensado y acondicionamiento propuesta en la Parte A, pero no modifica el objetivo de capturar las variaciones del volumen sanguíneo periférico ni el posterior cálculo del SPI. La guía sí lista esos componentes como parte de los materiales del estudiante y describe la construcción del circuito en la Parte A.

# 5. Procedimiento

# 5.1 Adquisición de la señal

A diferencia de la guía, donde se propone construir un circuito analógico para capturar las variaciones del volumen sanguíneo periférico, en este trabajo se conectó un sensor MAX30102 a una placa Arduino UNO  para registrar la señal PPG del dedo del sujeto de prueba. Esta modificación se realizó con el fin de obtener una señal más estable y reducir el tiempo asociado al diseño del acondicionamiento analógico.

<img width="558" height="430" alt="image" src="https://github.com/user-attachments/assets/aa999ed5-8eaa-4dae-a2a4-5598f5b5c9ab" />

Figura 1.Imagen del circuito.

# 5.2 Captura y visualización

Se programó la tarjeta Arduino UNO para enviar los datos del sensor al computador con el siguiente código.

```
#include <Wire.h>
#include "MAX30105.h"

MAX30105 sensor;

void setup() {
  Serial.begin(115200);
  delay(1000);
  Wire.begin();

  if (!sensor.begin(Wire, I2C_SPEED_STANDARD)) {
    while (1) {
      Serial.println(0);
      delay(200);
    }
  }

  byte ledBrightness = 15;
  byte sampleAverage = 4;
  byte ledMode = 2;
  int sampleRate = 100;
  int pulseWidth = 118;
  int adcRange = 2048;

  sensor.setup(ledBrightness, sampleAverage, ledMode, sampleRate, pulseWidth, adcRange);

  sensor.setPulseAmplitudeRed(0x0F);
  sensor.setPulseAmplitudeIR(0x0F);
  sensor.setPulseAmplitudeGreen(0);
}

void loop() {
  long ir = sensor.getIR();

  // solo una señal para el Serial Plotter
  Serial.println(-ir);

  delay(5);
}
```

Posteriormente, mediante MATLAB, se capturó la señal PPG durante un tiempo determinado, siguiendo la lógica de la guía, la cual solicita que el código permita capturar la señal, calcular el SPI con cada pulsación o latido y mostrar el resultado durante un tiempo finito definido por el usuario, mediante este código.

```
clear; clc; close all;

%% =========================================================
%  PARÁMETROS PRINCIPALES
%  =========================================================
puerto = "COM3";
baud   = 115200;

adjustSec  = 10;     % primeros 10 s: ajuste, NO se guardan
captureSec = 40;     % <<< CAMBIA AQUÍ el tiempo real de captura (s)

windowSec = 10;      % ventana visual en vivo
fpsPlot   = 20;      % refresco gráfico
alphaY    = 0.08;    % suavizado del eje Y
fsNominal = 100;     % Hz estimados para inicializar el detector

if captureSec <= 0
    error('captureSec debe ser mayor que 0.');
end

% Umbral inicial y límites del detector
threshold0 = max(3, round(0.006 * fsNominal));   % a 100 Hz -> 6
minThr     = max(3, round(0.03 * fsNominal));
maxThr     = max(8, round(0.20 * fsNominal));

% Buffers
NmaxLive = max(3000, ceil(3 * fsNominal * windowSec));
growCap  = max(2000, ceil(fsNominal * 10));

% Para calibrar el umbral durante el ajuste
calibThr = nan(1, 200);
nCalib   = 0;

%% =========================================================
%  SERIAL
%  =========================================================
s = serialport(puerto, baud);
configureTerminator(s, "LF");
flush(s);

%% =========================================================
%  DETECTOR MMPD
%  =========================================================
det = initMMPD(threshold0, minThr, maxThr);

%% =========================================================
%  FIGURA EN VIVO
%  =========================================================
figLive = figure( ...
    'Name', 'PPG en vivo + detección de picos', ...
    'Color', 'w', ...
    'NumberTitle', 'off');

axLive = axes(figLive);
hold(axLive, 'on');

hSig = plot(axLive, nan, nan, 'k-', 'LineWidth', 1.4);
hPk  = plot(axLive, nan, nan, 'ro', 'MarkerSize', 6, 'LineWidth', 1.2);

grid(axLive, 'off');
xlabel(axLive, 'Tiempo (s)');
ylabel(axLive, 'PPG');
title(axLive, 'Ajustando señal...');
xlim(axLive, [0 adjustSec]);
ylim(axLive, [0 1]);

%% =========================================================
%  BUFFERS VISUALES
%  =========================================================
xLive   = nan(1, NmaxLive);
yLive   = nan(1, NmaxLive);
nLive   = 0;

xPkLive = nan(1, NmaxLive);
yPkLive = nan(1, NmaxLive);
nPkLive = 0;

currentWindow = 0;
yMinSmooth = NaN;
yMaxSmooth = NaN;

%% =========================================================
%  BUFFERS DE CAPTURA FINAL
%  =========================================================
tCapAll  = nan(1, growCap);
yCapAll  = nan(1, growCap);
nCap     = 0;

tPkAll   = nan(1, growCap);
yPkAll   = nan(1, growCap);
nPk      = 0;

%% =========================================================
%  CONTROL DE TIEMPO
%  =========================================================
t0 = tic;
lastPlot = tic;

captureStarted  = false;
captureFinished = false;
userStopped     = false;
tCapture0       = NaN;
tCapCurrent     = 0;

fprintf('Ajustando señal durante %.1f s...\n', adjustSec);
fprintf('Luego se capturará durante %.1f s.\n', captureSec);

%% =========================================================
%  BUCLE PRINCIPAL
%  =========================================================
while ~captureFinished

    if ~isvalid(figLive)
        userStopped = true;
        break;
    end

    gotData = false;

    while s.NumBytesAvailable > 0
        gotData = true;

        dato  = readline(s);
        valor = str2double(strtrim(dato));

        if isnan(valor) || ~isfinite(valor)
            continue;
        end

        % Si tu señal viene invertida, descomenta:
        % valor = -valor;

        tAbs = toc(t0);

        %% =================================================
        %  FASE 1: AJUSTE INICIAL (NO GUARDAR)
        %  =================================================
        if ~captureStarted && tAbs < adjustSec

            [det, peakDetected, tPeakAbs, yPeak] = mmpdStep(det, valor, tAbs);

            if nLive < NmaxLive
                nLive = nLive + 1;
                xLive(nLive) = tAbs;
                yLive(nLive) = valor;
            end

            if peakDetected && tPeakAbs >= 0 && tPeakAbs <= adjustSec
                if nPkLive < NmaxLive
                    nPkLive = nPkLive + 1;
                    xPkLive(nPkLive) = tPeakAbs;
                    yPkLive(nPkLive) = yPeak;
                end

                if nCalib < numel(calibThr)
                    nCalib = nCalib + 1;
                    calibThr(nCalib) = det.threshold;
                end
            end

        %% =================================================
        %  TRANSICIÓN A CAPTURA
        %  =================================================
        elseif ~captureStarted
            captureStarted = true;
            tCapture0 = tAbs;
            tCapCurrent = 0;
            currentWindow = 0;

            % Calibrar umbral con los últimos valores del ajuste
            if nCalib >= 3
                idx0 = max(1, nCalib - 4);   % últimas 5 observaciones
                thCal = median(calibThr(idx0:nCalib), 'omitnan');
            else
                thCal = det.threshold;
            end

            thCal = min(max(thCal, minThr), maxThr);

            % Reiniciar detector limpio para la captura
            det = initMMPD(thCal, minThr, maxThr);
            det.prevSample = valor;
            det.prevTime   = tAbs;

            % Reiniciar buffers visuales
            xLive(:)   = nan; yLive(:)   = nan; nLive = 0;
            xPkLive(:) = nan; yPkLive(:) = nan; nPkLive = 0;
            yMinSmooth = NaN;
            yMaxSmooth = NaN;

            fprintf('Captura iniciada. Umbral calibrado = %.2f\n', thCal);

            % Guardar la primera muestra de captura en t=0
            nCap = nCap + 1;
            tCapAll(nCap) = 0;
            yCapAll(nCap) = valor;

            nLive = nLive + 1;
            xLive(nLive) = 0;
            yLive(nLive) = valor;

        %% =================================================
        %  FASE 2: CAPTURA REAL
        %  =================================================
        else
            tCap = tAbs - tCapture0;
            tCapCurrent = tCap;

            if tCap > captureSec
                captureFinished = true;
                break;
            end

            [det, peakDetected, tPeakAbs, yPeak] = mmpdStep(det, valor, tAbs);

            % Guardar señal capturada completa
            if nCap >= numel(tCapAll)
                tCapAll = [tCapAll nan(1, growCap)];
                yCapAll = [yCapAll nan(1, growCap)];
            end

            nCap = nCap + 1;
            tCapAll(nCap) = tCap;
            yCapAll(nCap) = valor;

            % Guardar picos detectados dentro del intervalo de captura
            if peakDetected
                tPeakCap = tPeakAbs - tCapture0;

                if tPeakCap >= 0 && tPeakCap <= captureSec
                    if nPk >= numel(tPkAll)
                        tPkAll = [tPkAll nan(1, growCap)];
                        yPkAll = [yPkAll nan(1, growCap)];
                    end

                    nPk = nPk + 1;
                    tPkAll(nPk) = tPeakCap;
                    yPkAll(nPk) = yPeak;
                end
            end

            % Ventanas visuales de 10 s durante la captura
            newWindow = floor(tCap / windowSec);

            if newWindow ~= currentWindow
                currentWindow = newWindow;
                xLive(:)   = nan; yLive(:)   = nan; nLive = 0;
                xPkLive(:) = nan; yPkLive(:) = nan; nPkLive = 0;
                yMinSmooth = NaN;
                yMaxSmooth = NaN;
            end

            % Guardar muestra visual de la ventana actual
            if nLive < NmaxLive
                nLive = nLive + 1;
                xLive(nLive) = tCap;
                yLive(nLive) = valor;
            end

            % Guardar picos visuales de la ventana actual
            if peakDetected
                tPeakCap = tPeakAbs - tCapture0;
                tWinStart = currentWindow * windowSec;
                tWinEnd   = tWinStart + windowSec;

                if tPeakCap >= tWinStart && tPeakCap < tWinEnd
                    if nPkLive < NmaxLive
                        nPkLive = nPkLive + 1;
                        xPkLive(nPkLive) = tPeakCap;
                        yPkLive(nPkLive) = yPeak;
                    end
                end
            end
        end
    end

    %% =====================================================
    %  REFRESCO FIGURA EN VIVO
    %  =====================================================
    if toc(lastPlot) >= 1/fpsPlot && isvalid(figLive)

        if ~captureStarted
            if nLive > 0
                set(hSig, 'XData', xLive(1:nLive), 'YData', yLive(1:nLive));
            else
                set(hSig, 'XData', nan, 'YData', nan);
            end

            if nPkLive > 0
                set(hPk, 'XData', xPkLive(1:nPkLive), 'YData', yPkLive(1:nPkLive));
            else
                set(hPk, 'XData', nan, 'YData', nan);
            end

            xlim(axLive, [0 adjustSec]);
            title(axLive, sprintf('Ajuste inicial (no se guarda): %.1f / %.1f s', ...
                min(toc(t0), adjustSec), adjustSec));

            if nLive > 5
                [yMinSmooth, yMaxSmooth] = smoothYLimits(yLive(1:nLive), yMinSmooth, yMaxSmooth, alphaY);
                ylim(axLive, [yMinSmooth yMaxSmooth]);
            end

        else
            tWinStart = currentWindow * windowSec;
            tWinEnd   = tWinStart + windowSec;

            if nLive > 0
                set(hSig, 'XData', xLive(1:nLive), 'YData', yLive(1:nLive));
            else
                set(hSig, 'XData', nan, 'YData', nan);
            end

            if nPkLive > 0
                set(hPk, 'XData', xPkLive(1:nPkLive), 'YData', yPkLive(1:nPkLive));
            else
                set(hPk, 'XData', nan, 'YData', nan);
            end

            xlim(axLive, [tWinStart tWinEnd]);
            title(axLive, sprintf('Capturando: %.1f / %.1f s  |  Ventana %.0f - %.0f s  |  umbral = %.2f', ...
                min(tCapCurrent, captureSec), captureSec, tWinStart, tWinEnd, det.threshold));

            if nLive > 5
                [yMinSmooth, yMaxSmooth] = smoothYLimits(yLive(1:nLive), yMinSmooth, yMaxSmooth, alphaY);
                ylim(axLive, [yMinSmooth yMaxSmooth]);
            end
        end

        drawnow nocallbacks;
        lastPlot = tic;
    end

    if ~gotData
        pause(0.001);
    end
end

%% =========================================================
%  CIERRE SERIAL
%  =========================================================
if exist('s', 'var')
    clear s
end

%% =========================================================
%  FIGURA FINAL CON LA SEÑAL CAPTURADA + PI
%  =========================================================
if nCap > 0
    tCapAll = tCapAll(1:nCap);
    yCapAll = yCapAll(1:nCap);

    if nPk > 0
        tPkAll = tPkAll(1:nPk);
        yPkAll = yPkAll(1:nPk);
    else
        tPkAll = [];
        yPkAll = [];
    end

    % Calcular índice fotopletismográfico
    [PIppg, ACmean, DCmean, tValleys, yValleys] = computePPGIndex(tCapAll, yCapAll, tPkAll);

    figFinal = figure( ...
        'Name', 'Señal capturada final + picos detectados + PI', ...
        'Color', 'w', ...
        'NumberTitle', 'off');

    axFinal = axes(figFinal);
    hold(axFinal, 'on');

    plot(axFinal, tCapAll, yCapAll, 'k-', 'LineWidth', 1.3);

    if ~isempty(tPkAll)
        plot(axFinal, tPkAll, yPkAll, 'ro', 'MarkerSize', 6, 'LineWidth', 1.2);
    end

    if ~isempty(tValleys)
        plot(axFinal, tValleys, yValleys, 'bo', 'MarkerSize', 4, 'LineWidth', 1.0);
    end

    grid(axFinal, 'off');
    xlabel(axFinal, 'Tiempo de captura (s)');
    ylabel(axFinal, 'PPG');

    if userStopped
        title(axFinal, sprintf('Captura interrumpida | Señal guardada: %.2f s | Picos detectados: %d', ...
            tCapAll(end), numel(tPkAll)));
    else
        title(axFinal, sprintf('Captura finalizada | Duración: %.2f s | Picos detectados: %d', ...
            tCapAll(end), numel(tPkAll)));
    end

    if ~isempty(yCapAll)
        yMin = min(yCapAll);
        yMax = max(yCapAll);

        if yMin == yMax
            yMin = yMin - 1;
            yMax = yMax + 1;
        end

        margen = 0.10 * (yMax - yMin);
        if margen <= 0
            margen = 1;
        end

        ylim(axFinal, [yMin - margen, yMax + margen]);
    end

    xlim(axFinal, [0 max(tCapAll(end), 1)]);

    if ~isnan(PIppg)
        txtPI = sprintf(['Indice fotopletismografico (PI) = %.2f %%\n' ...
                         'AC medio = %.4f\n' ...
                         'DC medio = %.4f'], ...
                         PIppg, ACmean, DCmean);
    else
        txtPI = sprintf(['Indice fotopletismografico (PI): no se pudo calcular\n' ...
                         'Verifica que la señal conserve componente DC']);
    end

    text(axFinal, 0.01, 0.98, txtPI, ...
        'Units', 'normalized', ...
        'VerticalAlignment', 'top', ...
        'HorizontalAlignment', 'left', ...
        'FontWeight', 'bold', ...
        'BackgroundColor', 'w', ...
        'Margin', 6);

    fprintf('Captura terminada.\n');
    fprintf('Tiempo capturado: %.2f s\n', tCapAll(end));
    fprintf('Picos detectados: %d\n', numel(tPkAll));

    if ~isnan(PIppg)
        fprintf('Indice fotopletismografico (PI): %.2f %%\n', PIppg);
        fprintf('AC medio: %.6f\n', ACmean);
        fprintf('DC medio: %.6f\n', DCmean);
    else
        fprintf('Indice fotopletismografico (PI): no se pudo calcular.\n');
    end
else
    warning('No se alcanzó a guardar señal útil de captura.');
end

%% =========================================================
%  FUNCIONES LOCALES
%  =========================================================

function det = initMMPD(threshold0, minThr, maxThr)
    det.prevSample = NaN;
    det.prevTime   = NaN;

    det.numUpsteps = 0;
    det.threshold  = threshold0;

    det.minThreshold = minThr;
    det.maxThreshold = maxThr;

    det.possiblePeak   = false;
    det.possibleValley = false;

    det.valuePossiblePeak   = NaN;
    det.timePossiblePeak    = NaN;

    det.valuePossibleValley = NaN;
    det.timePossibleValley  = NaN;
end

function [det, peakDetected, tPeak, yPeak] = mmpdStep(det, sample, tNow)

    peakDetected = false;
    tPeak = NaN;
    yPeak = NaN;

    if isnan(det.prevSample)
        det.prevSample = sample;
        det.prevTime   = tNow;
        return;
    end

    % Señal en ascenso
    if sample > det.prevSample

        det.numUpsteps = det.numUpsteps + 1;

        if ~det.possibleValley
            det.possibleValley = true;
            det.valuePossibleValley = det.prevSample;
            det.timePossibleValley  = det.prevTime;
        end

    % La señal deja de subir
    else

        if det.numUpsteps >= det.threshold
            det.possiblePeak = true;
            det.valuePossiblePeak = det.prevSample;
            det.timePossiblePeak  = det.prevTime;
        else
            if det.possibleValley
                if sample <= det.valuePossibleValley
                    det.valuePossibleValley = sample;
                    det.timePossibleValley  = tNow;
                end
            end
        end

        if det.possiblePeak
            if det.prevSample > det.valuePossiblePeak
                yPeak = det.prevSample;
                tPeak = det.prevTime;
            else
                yPeak = det.valuePossiblePeak;
                tPeak = det.timePossiblePeak;
            end

            peakDetected = true;

            if det.possibleValley
                det.possibleValley = false;
            end

            thrNew = 0.6 * det.numUpsteps;
            det.threshold = min(max(thrNew, det.minThreshold), det.maxThreshold);

            det.possiblePeak = false;
        end

        det.numUpsteps = 0;
    end

    det.prevSample = sample;
    det.prevTime   = tNow;
end

function [yMinSmooth, yMaxSmooth] = smoothYLimits(yData, yMinSmooth, yMaxSmooth, alphaY)

    yMin = min(yData);
    yMax = max(yData);

    if yMin == yMax
        yMin = yMin - 1;
        yMax = yMax + 1;
    end

    margen = 0.10 * (yMax - yMin);
    if margen <= 0
        margen = 1;
    end

    yMinTarget = yMin - margen;
    yMaxTarget = yMax + margen;

    if isnan(yMinSmooth)
        yMinSmooth = yMinTarget;
        yMaxSmooth = yMaxTarget;
    else
        yMinSmooth = (1 - alphaY) * yMinSmooth + alphaY * yMinTarget;
        yMaxSmooth = (1 - alphaY) * yMaxSmooth + alphaY * yMaxTarget;
    end
end

function [PIppg, ACmean, DCmean, tValleys, yValleys] = computePPGIndex(tSig, ySig, tPeaks)

    PIppg    = NaN;
    ACmean   = NaN;
    DCmean   = NaN;
    tValleys = [];
    yValleys = [];

    if isempty(tSig) || isempty(ySig) || numel(tPeaks) < 2
        return;
    end

    % Componente DC aproximada
    DCmean = mean(ySig, 'omitnan');

    if isnan(DCmean) || abs(DCmean) < eps
        return;
    end

    % Convertir tiempos de picos a índices
    peakIdx = nan(size(tPeaks));
    for i = 1:numel(tPeaks)
        [~, peakIdx(i)] = min(abs(tSig - tPeaks(i)));
    end

    peakIdx = unique(peakIdx(~isnan(peakIdx)));

    if numel(peakIdx) < 2
        return;
    end

    amps = [];

    % Valle entre cada par de picos consecutivos
    for i = 1:numel(peakIdx)-1
        i1 = peakIdx(i);
        i2 = peakIdx(i+1);

        if i2 <= i1 + 1
            continue;
        end

        [vmin, loc] = min(ySig(i1:i2));
        idxVal = i1 + loc - 1;

        % Amplitud pulsátil del latido
        amp = ySig(i2) - vmin;

        if isfinite(amp) && amp > 0
            amps(end+1) = amp; %#ok<AGROW>
            tValleys(end+1) = tSig(idxVal); %#ok<AGROW>
            yValleys(end+1) = vmin; %#ok<AGROW>
        end
    end

    if isempty(amps)
        return;
    end

    ACmean = mean(amps, 'omitnan');

    if isnan(ACmean) || ACmean <= 0
        return;
    end

    PIppg = 100 * (ACmean / abs(DCmean));
end
```


# 5.3 Protocolo experimental

Se realizó una captura total de 40 segundos, en concordancia con la guía. El sujeto de prueba permaneció en reposo en la primera prueba y en la segunda prueba se aplicó la maniobra Cold Pressor Test(splicando hielo sobre el brazo del sujeto de prueba), y en la ultima prueba 40 segundos el sujeto de prueba retornó a condiciones basales. 

# 6. Resultados

<img width="1600" height="1200" alt="image" src="https://github.com/user-attachments/assets/3321ca3d-a211-41f4-9ea0-7aadc2248e40" />


Figura 2.Gráfica de la señal PPG adquirida con el MAX30102.

<img width="1600" height="1200" alt="image" src="https://github.com/user-attachments/assets/02bb320b-ae72-46bc-8a26-ca3ec0ce9045" />

Figura 3.Detección de máximos y mínimos sobre la señal.


El sistema implementado permitió capturar la señal fotopletismográfica periférica de manera continua mediante el sensor MAX30102 durante 40 s. La señal obtenida presentó una morfología pulsátil identificable, lo que permitió detectar máximos y mínimos asociados a cada latido. A partir de estas características se estimó el SPI en tiempo real y se observó su variación a lo largo del protocolo experimental. Durante la fase basal se registraron valores relativamente estables; durante la maniobra Cold Pressor Test se observó un incremento del índice, mientras que en la fase final de recuperación los valores tendieron a aproximarse nuevamente a la línea base.


SPI promedio en reposo: 0.37%
SPI promedio durante CPT: 0.54%
SPI promedio en recuperación: 0.42%

# 7. Análisis de resultados

Los resultados obtenidos evidencian que el sistema implementado con el sensor MAX30102 logró registrar de forma adecuada la señal PPG, detectar de manera consistente los picos sistólicos y generar una estimación temporal del índice SPI. En particular, se obtuvo un SPI promedio en reposo de 0.37%, un SPI promedio durante el Cold Pressor Test (CPT) de 0.54% y un SPI promedio en recuperación de 0.42%, observándose así un incremento del índice durante la maniobra y una posterior disminución en la fase de recuperación. Este comportamiento es fisiológicamente coherente con una mayor activación simpática frente a un estímulo nocivo o estresante. Sin embargo, estos valores deben interpretarse con cautela, ya que la práctica se desarrolló en condiciones ambulatorias y en un sujeto consciente, por lo que no son directamente equivalentes a los observados en pacientes bajo anestesia general, contexto en el cual la guía menciona que el rango objetivo de SPI suele situarse entre 20 y 50. Como fortaleza, el uso del MAX30102 simplificó la etapa de adquisición y permitió obtener una señal estable, con menor dependencia de ajustes manuales del acondicionamiento analógico; no obstante, esta misma integración limita el análisis detallado de la etapa electrónica de captura planteada en la guía. En conjunto, el sistema demuestra un alcance importante para detectar variaciones fisiológicas asociadas a nocicepción y estrés, pero presenta limitaciones para cuantificar dolor de forma absoluta, puesto que el SPI es un índice indirecto que refleja cambios del balance autonómico y no la experiencia subjetiva del dolor en sí misma.

# 8. Conclusiones

Se desarrolló satisfactoriamente un sistema ambulatorio para la captura de la señal fotopletismográfica (PPG) y la estimación del índice SPI, cumpliendo así el objetivo general de la práctica. En lugar del circuito analógico propuesto en la guía, se implementó el sensor integrado MAX30102, lo que permitió simplificar el montaje experimental, reducir la complejidad del acondicionamiento electrónico y obtener una adquisición más estable de la señal.

La señal registrada permitió identificar adecuadamente los pulsos y extraer características útiles para el cálculo del SPI en tiempo real. Además, durante la maniobra Cold Pressor Test se observó una variación del índice consistente con la respuesta fisiológica esperada ante un estímulo estresante o nocivo, lo que confirma que el sistema es sensible a cambios del balance autonómico.

Finalmente, la práctica permitió comprender que dolor y nocicepción no son conceptos equivalentes. Mientras la nocicepción puede inferirse de manera indirecta a partir de señales fisiológicas como la PPG y de índices derivados como el SPI, el dolor incluye además una dimensión subjetiva, sensorial y emocional que no puede ser representada completamente mediante un único parámetro fisiológico. Por ello, el SPI debe entenderse como una herramienta de apoyo para la evaluación objetiva de la respuesta nociceptiva, pero no como una medida absoluta del dolor.

# 9. Preguntas para la discusión

# 9.1 ¿Cómo se relacionan las variaciones del volumen sanguíneo periférico con el balance autonómico?

Las variaciones del volumen sanguíneo periférico reflejadas en la señal PPG están moduladas por cambios en el tono vascular y en la actividad cardíaca. Cuando aumenta la actividad simpática, como ante estrés o estímulo nocivo, puede presentarse vasoconstricción periférica y alteraciones en la amplitud e intervalo de los pulsos. Por eso la PPG puede usarse como una señal indirecta del balance autonómico.

# 9.2 ¿Cómo se compara el SPI con otros índices como ANI o índice de perfusión?

El SPI se basa principalmente en características derivadas de la onda de pulso periférica y de la respuesta cardiovascular. El ANI, en cambio, se relaciona más con la variabilidad de la frecuencia cardíaca y el componente parasimpático. El índice de perfusión describe la fuerza de la señal pulsátil periférica, pero no fue diseñado específicamente como índice de nocicepción. En ese sentido, el SPI busca integrar información fisiológica para aproximarse al equilibrio nocicepción-analgesia.

# 10. Bibliografía

Oh, S. K. (2023). Surgical pleth index monitoring in perioperative pain management: current perspectives. Korean Journal of Anesthesiology.

Park, J., et al. (2022). Photoplethysmogram Analysis and Applications: An Integrative Review. Bioengineering.

Korhonen, I., & Yli-Hankala, A. (2009). Photoplethysmography and nociception. Acta Anaesthesiologica Scandinavica.

Ledowski, T., et al. (2019). Surgical pleth index: prospective validation of the score to predict moderate-to-severe postoperative pain. British Journal of Anaesthesia.

Won, Y. J., et al. (2018). Usefulness of surgical pleth index-guided analgesia during general anesthesia: a systematic review and meta-analysis. Journal of International Medical Research.

Analog Devices / Maxim Integrated. MAX30102 High-Sensitivity Pulse Oximeter and Heart-Rate Sensor Datasheet.

Wirch, J. L., et al. (2006). Cold pressor test protocol to evaluate cardiac autonomic function. Applied Physiology, Nutrition, and Metabolism.

Mourot, L., et al. (2009). Effects of the cold pressor test on cardiac autonomic control in normal subjects. Physiological Research.

Lovallo, W. (1975). The cold pressor test and autonomic function: a review and integration. Psychophysiology.

Guía de laboratorio: Instr_Biomed_Biosensor - Laboratorio 3
