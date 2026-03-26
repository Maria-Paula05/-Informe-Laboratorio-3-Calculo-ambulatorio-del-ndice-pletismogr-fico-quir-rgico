# Informe Laboratorio 3 : Calculo ambulatorio del índice pletismográfico quirúrgico

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

Reconocer las características fundamentales de la onda de pulso a partir de las cuales se obtiene el SPI.
Implementar un sistema de adquisición de señal PPG usando el sensor MAX30102 en lugar del circuito analógico propuesto en la guía.
Desarrollar un algoritmo en MATLAB capaz de capturar la señal, detectar máximos y mínimos y calcular el SPI en tiempo real.
Evaluar el comportamiento del SPI en reposo y durante una maniobra fisiológica tipo Cold Pressor Test.
Analizar el alcance y las limitaciones del sistema implementado para la estimación indirecta de nocicepción.
Estos objetivos conservan la intención de la guía, que pide reconocer las características de la onda de pulso, construir un sistema que calcule el SPI en tiempo real y validarlo con una maniobra similar al dolor agudo.

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

# 4.2 Aclaración sobre la modificación del montaje

En la guía original se solicita construir un circuito en proto-board con resistencias, condensadores, transistor 2N3904, sensor de reflectancia TCRT1000 o TCST110 y amplificador operacional LM358. En este informe no se utilizará ese circuito, sino un sensor MAX30102 para adquirir directamente la señal PPG. Esta adaptación reemplaza específicamente la etapa de sensado y acondicionamiento propuesta en la Parte A, pero no modifica el objetivo de capturar las variaciones del volumen sanguíneo periférico ni el posterior cálculo del SPI. La guía sí lista esos componentes como parte de los materiales del estudiante y describe la construcción del circuito en la Parte A.

# 5. Procedimiento
# 5.1 Adquisición de la señal

A diferencia de la guía, donde se propone construir un circuito analógico para capturar las variaciones del volumen sanguíneo periférico, en este trabajo se conectó un sensor MAX30102 a una placa Arduino UNO  para registrar la señal PPG del dedo del sujeto de prueba. Esta modificación se realizó con el fin de obtener una señal más estable y reducir el tiempo asociado al diseño del acondicionamiento analógico.

Figura 1.Imagen del circuito.

# 5.2 Captura y visualización

Se programó la tarjeta Arduino UNO para enviar los datos del sensor al computador con el siguiente código.


Posteriormente, mediante MATLAB, se capturó la señal PPG durante un tiempo determinado, siguiendo la lógica de la guía, la cual solicita que el código permita capturar la señal, calcular el SPI con cada pulsación o latido y mostrar el resultado durante un tiempo finito definido por el usuario, mediante este código.




# 5.3 Procesamiento de la señal

Una vez adquirida la señal, se aplicó un procesamiento digital orientado a identificar máximos y mínimos en cada pulso. Con estas características se estimaron los parámetros necesarios para el cálculo del SPI. La guía pide explícitamente usar un algoritmo de detección de máximos y mínimos y visualizar la evolución del SPI en función del tiempo.

Código...

# 5.4 Protocolo experimental

Se realizó una captura total de 2 minutos, en concordancia con la guía. Durante los primeros 40 segundos, el voluntario permaneció en reposo. En los siguientes 40 segundos, se aplicó la maniobra Cold Pressor Test, y en los últimos 40 segundos el voluntario retornó a condiciones basales. Durante todo el proceso se registró la evolución temporal del SPI para comparar su comportamiento antes, durante y después del estímulo. Ese esquema temporal está descrito en la Parte B de la guía.

# 6. Resultados


Gráfica de la señal PPG adquirida con el MAX30102.
Detección de máximos y mínimos sobre la señal.
Serie temporal del SPI durante los 2 minutos de captura.
Tabla con valores promedio de SPI en reposo, durante CPT y en recuperación.



El sistema implementado permitió capturar la señal fotopletismográfica periférica de manera continua mediante el sensor MAX30102. La señal obtenida presentó una morfología pulsátil identificable, lo que permitió detectar máximos y mínimos asociados a cada latido. A partir de estas características se estimó el SPI en tiempo real y se observó su variación a lo largo del protocolo experimental. Durante la fase basal se registraron valores relativamente estables; durante la maniobra Cold Pressor Test se observó un incremento del índice, mientras que en la fase final de recuperación los valores tendieron a aproximarse nuevamente a la línea base.


SPI promedio en reposo: ___
SPI promedio durante CPT: ___
SPI promedio en recuperación: ___

# 7. Análisis de resultados

La guía pide comparar los valores obtenidos con los que frecuentemente se observan durante cirugía y evaluar el alcance y las limitaciones del sistema para cuantificar dolor.

Los resultados obtenidos muestran que el sistema basado en MAX30102 fue capaz de registrar la señal PPG y generar una estimación temporal del SPI. El incremento observado durante la maniobra Cold Pressor Test es coherente con la expectativa fisiológica de un aumento en la respuesta simpática frente a un estímulo nocivo o estresante. Sin embargo, debe tenerse en cuenta que esta práctica se desarrolló en condiciones ambulatorias y en un sujeto de prueba consciente, por lo que los valores no son directamente equivalentes a los observados en pacientes bajo anestesia general, contexto en el cual la guía menciona que el rango objetivo de SPI suele situarse entre 20 y 50.

Una fortaleza del sistema implementado fue el uso del sensor MAX30102, ya que permitió simplificar la etapa de adquisición y obtener una señal con menor dependencia de ajustes manuales del acondicionamiento analógico. Como limitación, el uso de un sensor integrado reduce la posibilidad de analizar detalladamente el diseño electrónico de la etapa de captura planteada en la guía. Adicionalmente, el SPI es un índice indirecto y no representa de forma absoluta la experiencia subjetiva del dolor, sino una aproximación basada en cambios fisiológicos relacionados con el balance autonómico y la nocicepción.

# 8. Conclusiones

Se desarrolló un sistema ambulatorio para la captura de señal PPG y la estimación del SPI, cumpliendo el objetivo general de la práctica. En lugar del circuito analógico propuesto por la guía, se empleó un sensor MAX30102, lo cual permitió simplificar el montaje y mejorar la estabilidad de la adquisición.

La señal obtenida permitió identificar pulsos y extraer características útiles para el cálculo del SPI en tiempo real. Durante la maniobra Cold Pressor Test se evidenció una variación del índice consistente con una respuesta fisiológica al estrés.

La práctica permitió comprender que dolor y nocicepción no son conceptos equivalentes: la nocicepción puede inferirse a partir de señales fisiológicas como la PPG, mientras que el dolor incluye una dimensión subjetiva que no puede capturarse completamente con un único índice.

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
