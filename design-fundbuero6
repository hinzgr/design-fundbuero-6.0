import streamlit as st
import numpy as np
from PIL import Image
from tensorflow.keras.models import load_model
import os
import base64

# ── Seiteneinstellungen ────────────────────────────────────────────────────────
st.set_page_config(page_title="Fundbüro", page_icon="🎒", layout="centered")

# ── Logo laden (logo.png im selben Ordner) ────────────────────────────────────
def logo_laden():
    logo_pfad = "logo.png"
    if os.path.exists(logo_pfad):
        with open(logo_pfad, "rb") as f:
            daten = f.read()
        b64 = base64.b64encode(daten).decode()
        return f'<img src="data:image/png;base64,{b64}" style="height:70px; display:block; margin-left:auto;">'
    else:
        return (
            '<div style="text-align:right; font-family:\'IM Fell English\',\'Palatino Linotype\',serif;'
            'font-size:13px; font-weight:bold; color:red; line-height:1.2; font-style:italic;">'
            'Katharineum<br>zu Lübeck<br>'
            '<span style="color:black; font-size:11px; font-style:italic;">TU ES</span>'
            '</div>'
        )

LOGO_HTML = logo_laden()

# ── CSS ───────────────────────────────────────────────────────────────────────
st.markdown("""
<style>
@import url('https://fonts.googleapis.com/css2?family=IM+Fell+English:ital@0;1&display=swap');

/* Hintergrund weiß */
.stApp { background-color: white; }

/* Abstände */
.block-container {
    padding-top: 1rem !important;
    padding-bottom: 1rem !important;
    padding-left: 1.5rem !important;
    padding-right: 1.5rem !important;
    max-width: 480px !important;
}

/* ── ALLE Texte: IM Fell English kursiv ── */
button, .stButton > button, p, div, span, input, textarea, label {
    font-family: 'IM Fell English', 'Palatino Linotype', serif !important;
    font-style: italic !important;
}

/* ── Startseiten-Buttons: groß, volle Breite ── */
.grosser-button {
    margin-bottom: 14px !important;
}
.grosser-button > button {
    font-family: 'IM Fell English', 'Palatino Linotype', serif !important;
    font-size: 32px !important;
    font-style: italic !important;
    width: 100% !important;
    height: 160px !important;
    background-color: white !important;
    color: black !important;
    border: 2.5px solid black !important;
    border-radius: 6px !important;
    text-align: left !important;
    padding-left: 28px !important;
    display: flex !important;
    align-items: center !important;
}
.grosser-button > button:hover {
    background-color: #f5f5f5 !important;
}

/* ── Fertig-Button türkis ── */
.fertig-button > button {
    font-family: 'IM Fell English', 'Palatino Linotype', serif !important;
    font-size: 22px !important;
    font-style: italic !important;
    background-color: #3dd6b5 !important;
    color: white !important;
    border: none !important;
    border-radius: 6px !important;
    width: 100% !important;
    height: 60px !important;
    margin-top: 10px !important;
}
.fertig-button > button:hover {
    background-color: #2bbfa0 !important;
}

/* ── Zurück-Button ── */
.zurueck-button > button {
    font-family: 'IM Fell English', 'Palatino Linotype', serif !important;
    font-size: 18px !important;
    font-style: italic !important;
    background-color: white !important;
    color: black !important;
    border: 2px solid black !important;
    border-radius: 4px !important;
    width: 100% !important;
    height: 50px !important;
    margin-top: 12px !important;
}

/* ── Eingabefelder ── */
.stTextInput > div > div > input,
.stTextArea > div > div > textarea {
    font-family: 'IM Fell English', 'Palatino Linotype', serif !important;
    font-style: italic !important;
    font-size: 20px !important;
    border: 2.5px solid black !important;
    border-radius: 4px !important;
    background-color: white !important;
    padding: 18px 16px !important;
    height: 70px !important;
}
.stTextArea > div > div > textarea {
    height: 90px !important;
}

/* ── File-Uploader ── */
.stFileUploader > div {
    border: 2.5px solid black !important;
    border-radius: 4px !important;
    background-color: white !important;
    padding: 10px !important;
}
.stFileUploader label,
.stFileUploader span,
.stFileUploader p {
    font-family: 'IM Fell English', 'Palatino Linotype', serif !important;
    font-style: italic !important;
    font-size: 20px !important;
}

/* ── Fundstück-Karte ── */
.karte {
    display: flex;
    flex-direction: row;
    align-items: flex-start;
    border: 2px solid black;
    border-radius: 6px;
    padding: 10px 14px;
    margin-bottom: 12px;
    background-color: white;
    gap: 14px;
}
.karte-bild {
    width: 64px;
    min-width: 64px;
    height: 64px;
    object-fit: contain;
}
.karte-text {
    display: flex;
    flex-direction: column;
}
.karte-name {
    font-family: 'IM Fell English', 'Palatino Linotype', serif;
    font-size: 19px;
    font-style: italic;
    font-weight: bold;
    text-decoration: underline;
    margin-bottom: 4px;
}
.karte-beschreibung {
    font-family: 'IM Fell English', 'Palatino Linotype', serif;
    font-size: 14px;
    font-style: italic;
    color: #222;
}

/* ── Suchfeld ── */
.stTextInput > div > div > input[placeholder="Suchen 🔍"] {
    font-size: 20px !important;
}

/* ── Labels & Streamlit-Elemente ausblenden ── */
.stTextInput label, .stTextArea label, .stFileUploader label { display: none !important; }
hr { display: none; }
#MainMenu, footer, header { visibility: hidden; }
div[data-testid="stVerticalBlock"] > div { margin-bottom: 0px !important; }
</style>
""", unsafe_allow_html=True)

# ── Modell laden ───────────────────────────────────────────────────────────────
@st.cache_resource
def modell_laden():
    model = load_model("keras_model.h5", compile=False)
    with open("labels.txt", "r") as f:
        labels = [line.strip() for line in f.readlines()]
    return model, labels

model, labels = modell_laden()

# ── KI: Bild erkennen ─────────────────────────────────────────────────────────
def gegenstand_erkennen(bild: Image.Image):
    bild = bild.convert("RGB").resize((224, 224))
    bild_array = np.asarray(bild, dtype=np.float32)
    bild_array = (bild_array / 127.5) - 1
    bild_array = np.expand_dims(bild_array, axis=0)
    vorhersage = model.predict(bild_array)
    index = np.argmax(vorhersage)
    return labels[index], float(vorhersage[0][index])

# ── Session State ──────────────────────────────────────────────────────────────
if "seite" not in st.session_state:
    st.session_state.seite = "start"
if "fundstuecke" not in st.session_state:
    st.session_state.fundstuecke = []  # Liste von Dicts: {name, beschreibung, bild_bytes}

# ── Hilfsfunktion: Bild als base64 ────────────────────────────────────────────
def bild_zu_base64(bild_bytes):
    return base64.b64encode(bild_bytes).decode()

# ══════════════════════════════════════════════════════════════════════════════
# SEITE: START
# ══════════════════════════════════════════════════════════════════════════════
def seite_start():
    # Logo oben rechts
    st.markdown(LOGO_HTML, unsafe_allow_html=True)
    st.markdown("<div style='height:18px'></div>", unsafe_allow_html=True)

    # Button: Suchen
    st.markdown('<div class="grosser-button">', unsafe_allow_html=True)
    if st.button("Suchen 🔍", key="btn_suchen"):
        st.session_state.seite = "suchen"
        st.rerun()
    st.markdown('</div>', unsafe_allow_html=True)

    # Button: Hochladen
    st.markdown('<div class="grosser-button">', unsafe_allow_html=True)
    if st.button("Hochladen 📤", key="btn_hochladen"):
        st.session_state.seite = "hochladen"
        st.rerun()
    st.markdown('</div>', unsafe_allow_html=True)

# ══════════════════════════════════════════════════════════════════════════════
# SEITE: HOCHLADEN
# ══════════════════════════════════════════════════════════════════════════════
def seite_hochladen():
    # Logo oben rechts
    st.markdown(LOGO_HTML, unsafe_allow_html=True)
    st.markdown("<div style='height:10px'></div>", unsafe_allow_html=True)

    # Foto hochladen
    hochgeladene_datei = st.file_uploader(
        "Foto hochladen 📤",
        type=["jpg", "jpeg", "png", "webp"],
        key="foto_upload",
        label_visibility="visible"
    )

    # Vorschau & KI-Erkennung
    erkannter_name = ""
    bild_bytes = None

    if hochgeladene_datei is not None:
        bild = Image.open(hochgeladene_datei)
        bild_bytes = hochgeladene_datei.getvalue()
        st.image(bild, use_container_width=True)
        erkannter_name, konfidenz = gegenstand_erkennen(bild)
        st.markdown(
            f'<p style="font-size:14px; color:#555; text-align:center;">'
            f'KI-Vorschlag: <b>{erkannter_name}</b> ({konfidenz*100:.1f}%)</p>',
            unsafe_allow_html=True
        )

    # Eingabefelder
    name = st.text_input(
        "Name des Objekts",
        placeholder="Name des Objekts",
        value=erkannter_name,
        key="eingabe_name"
    )
    beschreibung = st.text_area(
        "Beschreibung",
        placeholder="Beschreibung",
        key="eingabe_beschreibung"
    )

    # Fertig-Button
    st.markdown('<div class="fertig-button">', unsafe_allow_html=True)
    if st.button("Fertig", key="btn_fertig"):
        if name.strip() == "":
            st.warning("Bitte gib einen Namen ein.")
        else:
            st.session_state.fundstuecke.append({
                "name": name.strip(),
                "beschreibung": beschreibung.strip(),
                "bild_bytes": bild_bytes
            })
            st.session_state.seite = "start"
            st.rerun()
    st.markdown('</div>', unsafe_allow_html=True)

    # Zurück-Button
    st.markdown('<div class="zurueck-button">', unsafe_allow_html=True)
    if st.button("← Zurück", key="btn_zurueck_hochladen"):
        st.session_state.seite = "start"
        st.rerun()
    st.markdown('</div>', unsafe_allow_html=True)

# ══════════════════════════════════════════════════════════════════════════════
# SEITE: SUCHEN
# ══════════════════════════════════════════════════════════════════════════════
def seite_suchen():
    # Suchfeld (kein Logo auf dieser Seite laut Mockup)
    suchbegriff = st.text_input(
        "Suchen",
        placeholder="Suchen 🔍",
        key="suchfeld"
    )

    # Gefilterte Fundstücke anzeigen
    gefiltert = [
        fs for fs in st.session_state.fundstuecke
        if suchbegriff.lower() in fs["name"].lower()
        or suchbegriff.lower() in fs["beschreibung"].lower()
    ]

    for fs in gefiltert:
        # Bild vorbereiten
        if fs["bild_bytes"]:
            b64 = bild_zu_base64(fs["bild_bytes"])
            bild_html = f'<img class="karte-bild" src="data:image/jpeg;base64,{b64}" />'
        else:
            bild_html = '<div class="karte-bild" style="background:#eee; border-radius:4px;"></div>'

        # Beschreibung als Zeilen
        beschreibung_html = ""
        if fs["beschreibung"]:
            zeilen = fs["beschreibung"].split("\n")
            for zeile in zeilen:
                beschreibung_html += f"- {zeile}<br>" if zeile.strip() else ""
        else:
            beschreibung_html = ""

        st.markdown(
            f'''
            <div class="karte">
                {bild_html}
                <div class="karte-text">
                    <div class="karte-name">{fs["name"]}</div>
                    <div class="karte-beschreibung">
                        Beschreibung:<br>{beschreibung_html}
                    </div>
                </div>
            </div>
            ''',
            unsafe_allow_html=True
        )

    if not gefiltert and suchbegriff == "":
        # Alle anzeigen wenn kein Suchbegriff
        for fs in st.session_state.fundstuecke:
            if fs["bild_bytes"]:
                b64 = bild_zu_base64(fs["bild_bytes"])
                bild_html = f'<img class="karte-bild" src="data:image/jpeg;base64,{b64}" />'
            else:
                bild_html = '<div class="karte-bild" style="background:#eee; border-radius:4px;"></div>'

            beschreibung_html = ""
            if fs["beschreibung"]:
                zeilen = fs["beschreibung"].split("\n")
                for zeile in zeilen:
                    beschreibung_html += f"- {zeile}<br>" if zeile.strip() else ""

            st.markdown(
                f'''
                <div class="karte">
                    {bild_html}
                    <div class="karte-text">
                        <div class="karte-name">{fs["name"]}</div>
                        <div class="karte-beschreibung">
                            Beschreibung:<br>{beschreibung_html}
                        </div>
                    </div>
                </div>
                ''',
                unsafe_allow_html=True
            )

    elif not gefiltert and suchbegriff != "":
        st.markdown(
            '<p style="text-align:center; color:#888; font-size:16px;">'
            'Kein Fundstück gefunden.</p>',
            unsafe_allow_html=True
        )

    # Zurück-Button
    st.markdown('<div class="zurueck-button">', unsafe_allow_html=True)
    if st.button("← Zurück", key="btn_zurueck_suchen"):
        st.session_state.seite = "start"
        st.rerun()
    st.markdown('</div>', unsafe_allow_html=True)

# ══════════════════════════════════════════════════════════════════════════════
# ROUTER
# ══════════════════════════════════════════════════════════════════════════════
if st.session_state.seite == "start":
    seite_start()
elif st.session_state.seite == "hochladen":
    seite_hochladen()
elif st.session_state.seite == "suchen":
    seite_suchen()
