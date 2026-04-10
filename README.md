# Sunwise3
import streamlit as st
import requests

# ==========================================
# CONFIGURACIÓN DE TU ANYTHINGLLM LOCAL
# ==========================================
# Reemplaza 'tu-slug-aqui' por el slug de tu Workspace (ej. tutor-solar)
API_URL = "http://localhost:3001/api/v1/workspace/tu-slug-aqui/chat"

# Pega aquí la API Key que generaste en el Paso 1
API_KEY = "K4M6XR5-ZTB4S0E-M7KX1SV-EEQYN0D"

# ==========================================
# DISEÑO DE LA PÁGINA WEB (STREAMLIT)
# ==========================================
st.set_page_config(page_title="SunWise", page_icon="☀️", layout="centered")

st.title("☀️ Tutor Comunitario de Energía Solar")
st.markdown("¡Hola! Soy tu asistente local. Pregúntame sobre instalación, baterías, o regulación en las ZNI.")

# Inicializar el historial de chat en la memoria
if "mensajes" not in st.session_state:
    st.session_state.mensajes = []

# Mostrar los mensajes anteriores en la pantalla
for mensaje in st.session_state.mensajes:
    with st.chat_message(mensaje["rol"]):
        st.markdown(mensaje["contenido"])

# Caja donde el usuario escribe
pregunta = st.chat_input("Escribe tu duda técnica aquí...")

if pregunta:
    # 1. Mostrar la pregunta del usuario en pantalla
    with st.chat_message("user"):
        st.markdown(pregunta)
    st.session_state.mensajes.append({"rol": "user", "contenido": pregunta})

    # 2. Conectarse a AnythingLLM (El Cerebro Offline)
    headers = {
        "Authorization": f"Bearer {API_KEY}",
        "Content-Type": "application/json",
        "accept": "application/json"
    }
    datos = {
        "message": pregunta,
        "mode": "chat" # Modo chat para que recuerde la conversación
    }

    # Mostrar un mensajito de "Pensando..."
    with st.spinner("Buscando en los manuales de la comunidad..."):
        try:
            # Enviamos la pregunta al puerto local 3001 (donde vive AnythingLLM)
            respuesta = requests.post(API_URL, headers=headers, json=datos)
            respuesta_json = respuesta.json()
            
            # Extraemos el texto de la respuesta
            respuesta_bot = respuesta_json.get("textResponse", "Lo siento, hubo un error al leer el manual.")
        except Exception as e:
            respuesta_bot = f"⚠️ Error de conexión: Verifica que AnythingLLM esté abierto en tu PC. Detalle: {e}"

    # 3. Mostrar la respuesta de la IA en pantalla
    with st.chat_message("assistant"):
        st.markdown(respuesta_bot)
    st.session_state.mensajes.append({"rol": "assistant", "contenido": respuesta_bot})
Chatbot solar
