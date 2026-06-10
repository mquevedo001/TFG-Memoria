# Checklist TFG por orden de aparición en la memoria

## Resumen / Introducción

- [x] **Matizar comparación con el estado del arte**
  - No afirmar comparación experimental directa con modelos SOTA si no se ejecutan Demucs, Spleeter u Open-Unmix en el mismo protocolo experimental.
  - Usar una formulación del tipo: *se evalúan los modelos propios y se contextualiza el trabajo frente a tecnologías actuales de separación de fuentes musicales*.

- [x] **Distinguir identificación y separación**
  - El sistema separa stems y los etiqueta por orden de salida.
  - No lo presentes como un clasificador general de instrumentos si no realiza clasificación explícita.
  - Puedes mantener “identificación” como objetivo amplio, pero en evaluación conviene hablar de separación de fuentes.

---

## 1. Planificación

### 1.1 Objetivos

- [x] **Verificar que los objetivos concretos quedan claramente identificados**
  - Objetivo principal: separar fuentes musicales.
  - Objetivos técnicos: implementar pipeline, entrenar modelos, comparar pérdidas, evaluar resultados.
  - Objetivo de interfaz: facilitar el uso y visualización del sistema.

- [x] **Comprobar que la parte de interfaz está en Objetivos, no en Alcance**
  - Tu apunte indica que ya has movido esto.
  - Revisa que Objetivos mencione carga de audio, selección de modelo, separación, visualización, descarga y uso didáctico.

### 1.2 Alcance

- [x] **Revisar definición de stems**
  - Debe quedar claro que un *stem* es una pista/fuente separada de una mezcla musical.
  - Mantener coherencia entre “fuente”, “stem”, “pista separada” y “elemento musical”.

- [x] **Revisar lista de pérdidas mencionadas**
  - No incluyas SI-SDR como función de pérdida si finalmente solo se usa como métrica.
  - Distinguir claramente:
    - pérdidas de entrenamiento;
    - métricas de evaluación;
    - pérdidas avanzadas consideradas pero no incluidas en la comparativa final.

### 1.3 Cronograma / 1.4 Desviaciones

- [x] **Mantener narrativa V1/V2/V3 como evolución metodológica**
  - V1: tratamiento de canales no suficientemente controlado.
  - V2: corrección a mono, pero evaluación/despliegue todavía no estabilizados.
  - V3: protocolo final validado con test fijo, baseline, inferencia común y trazabilidad.

- [x] **Explicar que las desviaciones no son solo retrasos**
  - Deben entenderse como parte del proceso de validación experimental.
  - Destacar que permitieron detectar errores que afectaban a la validez de los resultados.

---

## 2. Herramientas utilizadas

- [x] **Revisar que cada herramienta tenga su papel técnico correcto**
  - PyTorch: modelo, tensores, entrenamiento, backpropagation.
  - nussl: audio, STFT/iSTFT, separación, métricas.
  - Ignite: bucles de entrenamiento, validación, checkpoints, early stopping.
  - Streamlit: interfaz.
  - torchaudio/librosa: carga, transformación o soporte de audio.
  - Matplotlib: visualización.

- [x] **Evitar atribuir funciones que realmente no se hayan usado**
  - Si una librería aparece, debe estar justificado su uso real en el proyecto.

---

## 3. Marco teórico

### Consideración general

- [x] **Mantener secciones pequeñas, pero conectar cada una con el proyecto**
  - Tu decisión de mantener conceptos breves es válida.
  - Añade al final de cada concepto una frase que explique por qué importa para separación musical o para tu pipeline.

- [] **Comprobar referencias cruzadas a figuras**
  - No usar números escritos a mano tipo “Fig. 4.2”.
  - Usar `\label{...}` y `\ref{...}`.
  - Tu apunte dice que ya has referenciado las figuras; revisa que no quede ninguna referencia manual.

---

## 3.1 Sonidos y señales

### Naturaleza del sonido

- [ ] **Mantener rango de audición humana en frecuencia/tono**
  - Está bien incluirlo.
  - Conectarlo con muestreo, Nyquist o representación frecuencial.

### Magnitud y fase

- [x] **Corregir matiz magnitud vs energía**
  - Magnitud = módulo del coeficiente complejo de la STFT.
  - Energía/potencia suele asociarse al cuadrado de la magnitud.

- [ ] **Decidir dónde ubicar la problemática de la fase**
  - Tu apunte indica que queda pendiente trasladar el comentario.
  - Ubicación recomendada:
    - En marco teórico: definir magnitud y fase.
    - En Parte 1, Inputs/targets: explicar que se estima magnitud y se reconstruye con fase de mezcla.
    - En Evaluación/Limitaciones: explicar que usar fase de mezcla limita la calidad.
  - No cargar demasiado la definición teórica con problemas de implementación.

- [ ] **Corregir frase sobre qué dato falta al reconstruir**
  - No falta la magnitud: la magnitud fuente se estima mediante la máscara.
  - Lo que no se conoce en inferencia es la fase real de cada fuente.

### STFT

- [x] **Confirmar acrónimo y traducción**
  - STFT = *Short-Time Fourier Transform*.
  - En castellano: Transformada de Fourier de tiempo corto.

- [x] **Explicar mejor el funcionamiento de la STFT**
  - La STFT aplica la DFT sobre ventanas temporales sucesivas de la señal.
  - La ventana no es necesariamente triangular.
  - En tu proyecto se usa `sqrt_hann`.

- [x] **Definir parámetros importantes**
  - `window_length`: tamaño de ventana.
  - `hop_length`: salto entre ventanas consecutivas.
  - solapamiento entre ventanas.
  - frame temporal.
  - bin de frecuencia.

- [x] **Aclarar la fórmula**
  - Cada celda tiempo-frecuencia es un coeficiente complejo.
  - No confundir “celda” con “ventana”.

### Espectrogramas / Representaciones digitales

- [x] **Revisar nueva ubicación de espectrogramas**
  - Tu apunte indica que los has movido a Representaciones digitales / formatos de audio.
  - Comprueba que no se duplica la explicación respecto a STFT.

- [x] **Añadir distinción STFT lineal vs Mel**
  - El proyecto usa espectrograma de magnitud STFT lineal.
  - No usa espectrogramas Mel como entrada principal.
  - Motivo: la STFT lineal mantiene una relación directa con la reconstrucción mediante iSTFT.
  - Mel puede ser útil para clasificación o tareas perceptuales, pero no encaja igual con reconstrucción de audio.

### Mezclas lineales

- [x] **Revisar explicación ampliada**
  - Tu apunte indica que ya has profundizado.
  - Mantener la fórmula como aproximación.
  - Añadir que las mezclas reales incluyen paneo, reverberación, efectos, retardos y procesamiento de mezcla.

### Mezclas coherentes e incoherentes

- [x] **Verificar corrección conceptual**
  - Tu apunte dice que ya has explicado mejor el concepto.
  - La coherencia se relaciona con correlación/dependencia temporal-frecuencial, fase y estructura común.
  - No reducirlo a que dos señales tengan tonos distintos.
  - No afirmar de forma absoluta que las incoherentes siempre son más fáciles de separar; depende de solapamiento y modelo.

### Separación ciega de fuentes

- [x] **Añadir distinción entre separación ciega y supervisada**
  - Separación ciega: intenta separar usando solo la mezcla observada.
  - Separación supervisada: aprende a partir de pares mezcla/fuentes.
  - Tu TFG es principalmente supervisado, porque entrenas con `source_magnitudes`.

---

## 3.2 Aprendizaje automático

### Dataset

- [x] **Mantener definición general de dataset, pero conectarla con tu caso**
  - Tu decisión es razonable: un lector no técnico puede necesitar la definición.
  - Añadir que en este proyecto hay:
    - conjunto de entrenamiento;
    - conjunto de validación;
    - conjunto de test fijo para evaluación final (`representative_test`).

### RNNs / LSTM

- [x] **Corregir parte pendiente de RNNs**
  - Tu apunte indica que aún falta.
  - Añadir que una LSTM es una variante de RNN diseñada para procesar secuencias y mantener memoria temporal.

- [x] **Distinguir estado de celda y estado oculto**
  - Estado de celda: `c_t`.
  - Estado oculto/salida: `h_t`.
  - En muchos diagramas LSTM, la línea superior representa el estado de celda.
  - Hecho en Parte1/ArqBase

- [x] **Explicar puertas de la LSTM**
  - Puerta de olvido.
  - Puerta de entrada.
  - Candidato de memoria.
  - Puerta de salida.
  - Hecho en Parte1/ArqBase

- [x] **Conectar LSTM con espectrogramas**
  - El espectrograma puede verse como secuencia temporal de frames.
  - La LSTM procesa esa secuencia para estimar máscaras.
  - Hecho en Parte1/ArqBase

### Funciones de pérdida

- [x] **Definir función de pérdida en marco teórico**
  - Es central en tu TFG.
  - La pérdida mide el error entre estimación y objetivo.
  - El optimizador ajusta el modelo minimizando esa pérdida.
  - Distintas pérdidas priorizan distintos tipos de error.

### Métricas de evaluación

- [x] **Añadir métricas de separación de audio**
  - No quedarse solo en precisión, recall, F1, AUC o matriz de confusión.
  - Incluir:
    - SI-SDR;
    - SDR/SIR/SAR si los mencionas;
    - media;
    - mediana;
    - desviación típica;
    - baseline;
    - evaluación auditiva como complemento.

---

## 4. Estado del arte

### Consideración general

- [x] **Revisar si se sobreexplica IA general**
  - Tu apunte indica dudas.
  - El capítulo debe centrarse en separación de fuentes musicales.
  - La parte de Asimov/Turing/AlphaGo, si permanece, debe ser breve y no desplazar el tema principal.

- [ ] **Mantener estructura inspirada en SigSep**
  - Recapitulación útil:
    - procesado de señal;
    - modelado armónico de señal principal;
    - modelado del acompañamiento;
    - métodos probabilísticos;
    - enfoques basados en datos;
    - aprendizaje profundo;
    - modelos/herramientas actuales.

- [x] **No igualar probabilístico y data-driven**
  - Un modelo probabilístico puede ser explícito y basado en hipótesis.
  - Un método data-driven aprende patrones desde datos.
  - No son equivalentes.

### 4.1 Separación de fuentes musicales

- [x] **Aclarar escenarios de separación**
  - 2 stems: voz / acompañamiento.
  - 4 stems: voz / bajo / batería / otros.
  - En 2 stems, acompañamiento suele ser suma de bajo + batería + otros.

### 4.2 Representación tiempo-frecuencia y máscaras

- [x] **Evitar llamar binarias a las máscaras del modelo**
  - Tu modelo predice máscaras suaves en `[0,1]`.
  - Solo una `ideal_binary_mask` es binaria.
  - Usar “máscaras suaves tiempo-frecuencia”.

### 4.3 Métodos clásicos

- [x] **Añadir o reforzar NMF / bajo rango**
  - Es un concepto clásico importante en separación musical.
  - Acompañamiento como estructura repetitiva o de bajo rango.
  - Voz como componente más variable o dispersa.

- [x] **Matizar HMM/GMM**
  - No siempre asignan directamente celdas TF.
  - Modelan estados o distribuciones que pueden ayudar a estimar fuentes o máscaras.

### 4.4 Aprendizaje profundo

- [x] **Matizar modelos waveform e híbridos**
  - No afirmar que no se usan por ser ineficientes.
  - Demucs/Hybrid Demucs son relevantes.
  - Mejor: requieren arquitecturas más complejas y mayor coste computacional.
  - Tu TFG usa STFT + máscaras por alcance, interpretabilidad y control experimental.

### 4.5 Herramientas y tecnologías actuales

- [x] **Separar herramientas/modelos de datasets/benchmarks**
  - Open-Unmix, Spleeter, Demucs/Hybrid Demucs: modelos o herramientas.
  - MUSDB18: dataset/benchmark.

- [x] **No presentar Spleeter como SOTA actual**
  - Es una herramienta práctica y relevante.
  - No necesariamente representa el estado más avanzado actual.

### 4.6 Posicionamiento del trabajo

- [x] **Revisar el alcance de la aportación**
  - Evitar “aportación al estado del arte” si suena demasiado fuerte.
  - Mejor: “estudio experimental controlado dentro del alcance del TFG”.
  - Reforzar que el objetivo no es superar SOTA, sino comparar pérdidas y construir un pipeline completo.

---

## 5. Parte 1: separación de fuentes

### 5.1 Diseño del modelo

- [x] **Corregir “máscaras binarias”**
  - Cambiar por “máscaras tiempo-frecuencia suaves”.
  - Solo hablar de binario si te refieres a una máscara ideal concreta.

### 5.2 Arquitectura base

- [x] **Corregir explicación LSTM**
  - La línea superior en el diagrama suele representar `c_t`, estado de celda.
  - `h_t` es estado oculto/salida.
  - Añadir que la bidireccionalidad usa contexto pasado y futuro del espectrograma.

### 5.3 Modificaciones propuestas

- [x] **Explicar modificación como coherencia de pipeline**
  - No presentarlo como “mala generalización” si el problema fue incompatibilidad entre checkpoint, configuración y forward.
  - Enfatizar que se estabilizó la inferencia para que coincidiese con el entrenamiento.

### 5.3 Función de pérdida: Deep-feature-EMD

- [x] **Completar matices conceptuales**
  - CNN auxiliar no preentrenada en música.
  - Pérdida experimental, no perceptual fuerte.
  - Activaciones aplanadas como distribuciones empíricas.
  - Sinkhorn como aproximación regularizada de EMD.

- [x] **Revisar citas usadas para deep features**
  - Una cita de radiografías puede servir para explicar deep features en general, pero no es ideal para separación musical.
  - Si la mantienes, deja claro que se usa como referencia general del concepto, no como referencia específica de audio musical.

### 5.4 Configuración de entrenamiento

#### Inputs y targets

- [x] **Corregir problemática de fase**
  - La fase que falta en inferencia es la fase real de cada fuente.
  - La magnitud se estima aplicando la máscara a la magnitud de mezcla.

- [x] **Añadir shapes principales**
  - Entrada mezcla: `[B,T,F]` o `[B,F,T]` antes de adaptar.
  - Targets: `[B,T,F,C,S]`.
  - Salidas: `mask` y `estimates`.

#### TFMs aplicadas

- [x] **Aclarar que TFMs significa transformaciones**
  - Evita confusión con Trabajo Fin de Máster.
  - Explicar:
    - STFT;
    - magnitud;
    - fase;
    - AmplitudeToDB;
    - BatchNorm;
    - reordenación dimensional.

- [ ] **Mencionar pérdidas con fase**
  - `lpsa_phase` y `l_mrs` requieren `mixture_phase` y/o `source_phase`.
  - Si finalmente se descartan, explicarlo como limitación técnica o decisión experimental.

#### Data augmentation

- [x] **No afirmar mejoras si está desactivado**
  - Decir que se contempló como técnica de generalización.
  - Explicar que se desactivó para mantener comparativa controlada.
  - Mencionarlo como línea futura.

#### Preprocesado del dataset

- [x] **Corregir `other.wav`**
  - MUSDB18 usa:
    - `mixture.wav`;
    - `vocals.wav`;
    - `bass.wav`;
    - `drums.wav`;
    - `other.wav`.

- [x] **Aclarar split**
  - Explicar si el 60/40 es train/validation.
  - Dejar claro que `representative_test` queda separado para evaluación final.
  - Evitar que parezca que el test final se mezcla con entrenamiento o validación.

### 5.5 Entrenamiento del modelo

- [ ] **Revisar lista de pérdidas entrenadas**
  - Debe coincidir con scripts, resultados y tablas.
  - Incluir solo lo que realmente has entrenado/evaluado o marcar como descartado.
  - Revisar presencia de:
    - `l1`;
    - `l2`;
    - `l1_freq`;
    - `l2_freq`;
    - `logl1`;
    - `logl2`;
    - `log_mag`;
    - `log_compressed_l2`;
    - `lpsa`;
    - `mask_l1`;
    - `deep_feature`;
    - `deep_feature_emd`;
    - `lpsa_phase`;
    - `l_mrs` si aparece como no evaluada.

- [x] **Reforzar que V3 es protocolo final**
  - No describirlo solo como otra tanda de entrenamiento.
  - Debe quedar claro que V3 introduce:
    - test fijo;
    - baseline;
    - `run_inference` común;
    - checkpoints organizados;
    - trazabilidad.

---

## 6. Parte 2: interfaz

### Introducción general de la Parte 2

- [x] **Añadir introducción de la interfaz**
  - La interfaz actúa como herramienta demostrativa y didáctica.
  - Permite aplicar modelos entrenados, visualizar resultados y facilitar el uso del sistema.

### 6.1 Objetivos

- [x] **Completar objetivos de la interfaz**
  - Cargar audio.
  - Seleccionar modelo.
  - Elegir función de pérdida/modelo entrenado.
  - Seleccionar número de stems.
  - Separar fuentes.
  - Reproducir resultados.
  - Descargar stems.
  - Visualizar waveform y espectrograma.
  - Opcionalmente entrenar un modelo propio.

### 6.2 Diseño de experiencia de usuario

- [x] **Completar organización y visualización**
  - Sidebar de configuración.
  - Zona principal de carga.
  - Consola/logs.
  - Reproductores.
  - Gráficas.
  - Botones de descarga.

- [ ] **Completar explicaciones y guías**
  - Explicar que la GUI acompaña con textos sobre parámetros, funciones de pérdida y modelos.
  - Justificar el valor didáctico de la interfaz.

### 6.3 Casos de uso

- [ ] **Completar casos de uso**
  - Separar pista externa con modelo preentrenado.
  - Usar modelo entrenado con una pérdida concreta.
  - Entrenar modelo propio.
  - Visualizar espectrogramas.
  - Descargar stems.

- [x] **Explicar entrenamiento parametrizable con cautela**
  - No presentarlo como proceso instantáneo.
  - Es una funcionalidad avanzada.
  - Lanza entrenamiento y muestra logs/resultados.

- [x] **Definir feedback**
  - Feedback = mensajes del sistema, logs, progreso, errores, rutas y resultados visuales.
  - No es feedback aprendido por el modelo.

---

## 7. Integración del sistema

### Backend

- [x] **Redactar sección Backend**
  - Introducción sugerida: describir cómo se conectan entrenamiento, inferencia, despliegue, evaluación e interfaz en un sistema único.
  - Incluir:
    - `main.py`;
    - `train.py`;
    - `deployment.py`;
    - `evaluate_models.py`;
    - `MaskInference`;
    - `config`;
    - carpetas de checkpoints;
    - carpetas de resultados.

### Comunicación entre módulos

- [x] **Redactar comunicación entre módulos**
  - Flujo recomendado:
    1. CLI o GUI modifica configuración.
    2. Se cargan datos o audio externo.
    3. Se calcula STFT.
    4. El modelo estima máscaras.
    5. `run_inference` reconstruye audio.
    6. Deployment guarda stems.
    7. Evaluación calcula métricas.
    8. Resultados se guardan en JSON/gráficas/tablas.

### Incidencias de integración y depuración

- [x] **Redactar incidencias**
  - Incluir:
    - checkpoint mismatch;
    - `strict=False` no soluciona shapes incompatibles;
    - forward incoherente entrenamiento/inferencia;
    - STFT/iSTFT con parámetros incompatibles;
    - mono/estéreo;
    - orden incorrecto de fuentes;
    - rutas relativas al ejecutar scripts desde `scripts`;
    - pérdidas con fase (`l_mrs`, `lpsa_phase`);
    - baseline para detectar modelos degenerados.

---

## 8. Evaluación del sistema

### 8.1 Métricas utilizadas

- [x] **Corregir interpretación de SI-SDR**
  - Mayor SI-SDR = mejor separación.
  - Valor bajo o negativo = peor estimación respecto a referencia.

- [x] **Explicar agregación**
  - Media: rendimiento promedio.
  - Mediana: robustez frente a outliers.
  - Desviación típica: estabilidad entre pistas.

- [x] **Explicar baseline**
  - Baseline = usar la mezcla como estimación de cada fuente.
  - Sirve como criterio mínimo.
  - Si un modelo no supera baseline, genera audio pero no demuestra separación útil.

### 8.2 Validación del pipeline de evaluación

- [x] **Pulir finalidad de la validación**
  - No demuestra que el modelo sea bueno.
  - Demuestra que la comparación entre modelos es homogénea, justa y trazable.

- [x] **Mantener protocolo claro**
  - `representative_test`.
  - Misma inferencia que deployment.
  - Conversión a mono.
  - Recorte a longitud común.
  - Orden fijo de fuentes.
  - SI-SDR.
  - JSON/summaries.
  - Comparación contra baseline.

### 8.3 Resultados obtenidos

- [ ] **Completar tablas**
  - Rellenar:
    - grupo;
    - checkpoint;
    - SI-SDR medio;
    - mediana;
    - desviación;
    - supera baseline;
    - estado si no evaluado.

- [x] **Recalcular desviaciones típicas**
  - Una desviación típica no debe ser negativa.
  - Revisar filas donde parezca copiado el SI-SDR medio.

- [ ] **Marcar `l_mrs` si se descarta**
  - Usar “No evaluado” o “Descartado”.
  - Explicar brevemente: coste, integración con fase, reconstrucción multirresolución.

### Discusión y análisis

- [ ] **Redactar discusión**
  - Introducción sugerida: interpretar resultados, no repetir tablas.
  - Comparar:
    - 2 stems vs 4 stems;
    - pérdidas principales vs experimentales;
    - mejores modelos;
    - modelos que no superan baseline;
    - estabilidad media/mediana/desviación;
    - deep_feature/deep_feature_emd;
    - lpsa/mask_l1;
    - impacto del coste computacional.

### Limitaciones y margen de mejora

- [x] **Redactar limitaciones**
  - Introducción sugerida: explicar límites técnicos que condicionan resultados sin invalidar el trabajo.
  - Incluir:
    - fase de mezcla;
    - arquitectura simple;
    - entrenamiento mono;
    - recursos computacionales;
    - dataset limitado;
    - coste de pérdidas avanzadas;
    - métricas objetivas frente a escucha perceptual;
    - interfaz demostrativa, no producto comercial final.

---

## 9. Conclusiones y trabajo futuro

### Síntesis del trabajo realizado

- [ ] **Redactar síntesis**
  - Revisión bibliográfica.
  - Implementación del modelo.
  - Entrenamiento con distintas pérdidas.
  - Evaluación con baseline.
  - Interfaz.
  - Evolución V1/V2/V3.
  - Lecciones técnicas.

### Líneas de mejora

- [ ] **Redactar líneas de mejora**
  - Arquitectura más potente.
  - Entrenamiento estéreo.
  - Estimación de fase.
  - Data augmentation.
  - Más datos.
  - Métricas perceptuales.
  - GUI más robusta.

### Trabajo futuro

- [ ] **Redactar trabajo futuro**
  - Comparación directa con Demucs, Spleeter y Open-Unmix.
  - Uso de MUSDB18/MDX completo.
  - Modelos híbridos waveform/espectrograma.
  - Extractor deep-feature entrenado en audio.
  - Separación en tiempo real.
  - Separación de más fuentes.

---

## 10. Anexos

### Código relevante

- [ ] **Incluir fragmentos seleccionados, no archivos completos**
  - Introducción sugerida: el anexo recoge fragmentos representativos del sistema implementado.
  - Para cada fragmento, explicar por qué es relevante.

- [ ] **Lista de código a incluir**
  - `main.py`: modos `train`, `eval`, `deploy`, `mix_generation`.
  - `MaskInference.forward`: magnitud → dB → BatchNorm → LSTM → Embedding → máscara → estimación.
  - `train.py`: entrenamiento, validación, early stopping y checkpoints.
  - `metrics.py`: funciones de pérdida.
  - `commons/inference.py`: inferencia común.
  - `commons/audio_utils.py`: carga de modelos e inferencia de arquitectura desde checkpoint.
  - `deployment.py`: aplicación a audio externo y guardado de stems.
  - `evaluate_models.py`: evaluación sistemática.
  - `evaluate_mixture_baseline.py`: baseline.
  - `GUI/gui.py`: interfaz, selección de modelo, carga, separación, visualización y descarga.

### Registro de incidencias técnicas

- [ ] **Crear tabla de incidencias**
  - Columnas:
    - incidencia;
    - síntoma/error;
    - causa;
    - solución;
    - impacto.

- [ ] **Incidencias a incluir**
  - Checkpoint mismatch.
  - `strict=False` no resolviendo incompatibilidad de shapes.
  - Forward distinto entre entrenamiento e inferencia.
  - STFT/iSTFT con parámetros incompatibles.
  - Problemas mono/estéreo.
  - Stems demasiado parecidos a mezcla.
  - Baseline.
  - Orden incorrecto de fuentes.
  - `l_mrs` / `lpsa_phase` y fases.
  - Rutas relativas en scripts.

### Configuraciones

- [ ] **Incluir configuración V3**
  - STFT window 512.
  - Hop 128.
  - `sqrt_hann`.
  - 257 bins.
  - Mono.
  - LSTM hidden size 50.
  - Bidireccional.
  - Sigmoid.
  - Batch sizes.
  - Learning rate.
  - Weight decay.
  - Early stopping.
  - Pérdidas entrenadas.
  - Rutas de checkpoints/resultados.

### Instrucciones posibles

- [ ] **Incluir comandos reproducibles**
  - Entrenamiento individual.
  - Entrenamiento batch.
  - Evaluación.
  - Baseline.
  - Deployment.
  - GUI.
  - Diagnóstico.
  - Ranking.

### Resultados adicionales

- [ ] **Añadir material complementario**
  - Rankings completos.
  - SI-SDR por pista.
  - Summaries JSON.
  - Curvas de pérdida.
  - Ejemplos de stems.
  - Máscaras.
  - Correlaciones mezcla/salida.

---

## Revisión final

- [ ] **Revisión de consistencia conceptual**
  - Máscaras suaves, no binarias.
  - Fase de mezcla como aproximación.
  - SI-SDR mayor = mejor.
  - `other.wav`, no `others.wav`.
  - Separación supervisada vs ciega.
  - STFT lineal, no Mel.

- [ ] **Comprobar secciones vacías**
  - Parte 2.
  - Integración.
  - Discusión.
  - Limitaciones.
  - Conclusiones.
  - Anexos.

- [ ] **Revisar tablas finales**
  - Sin celdas vacías.
  - Sin desviaciones negativas.
  - Baseline claro.
  - Pérdidas no evaluadas marcadas explícitamente.

- [ ] **Compilar y revisar PDF**
  - Borrar auxiliares si hace falta.
  - Compilar con Biber.
  - Revisar índice, figuras, tablas, bibliografía y PDF final.
