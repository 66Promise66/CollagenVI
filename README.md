import streamlit as st
import numpy as np
import pandas as pd
import plotly.graph_objects as go

st.set_page_config(
    page_title="Collagen 6A3 Myopathy Simulator",
    page_icon="🧬",
    layout="wide",
    initial_sidebar_state="expanded",
)

st.markdown(
    """
    <style>
    .main {background: linear-gradient(135deg, #0d102b 55%, #181b25 100%);}
    .css-1v0mbdj {background: #141627;}
    h1, h2, h3, h4, h5, h6 {color: #a3c2fd !important;}
    .stTabs [data-baseweb="tab"] {background: #192041 !important; color: #ffe066 !important;}
    .stTabs [aria-selected="true"] {background: #21264b !important; color: #ff5050 !important;}
    .stProgress > div > div {background-image: linear-gradient(90deg,#1e2a74,#a3c2fd);}
    .stButton>button {background-color: #21264b; color: #ffe066;}
    </style>
    """,
    unsafe_allow_html=True
)

st.markdown(
    """
    <h1 style='text-align: center; color:#a3c2fd;'>🧬 Collagen 6A3 Congenital Myopathy Simulator</h1>
    <h3 style='text-align: center; color:#ffa500;'>Modeling Gene Expression, Muscle Weakness, and Regeneration</h3>
    """,
    unsafe_allow_html=True
)

st.markdown("---", unsafe_allow_html=True)

with st.expander("ℹ️ What does this app do?"):
    st.markdown(
        """
        This interactive simulation models gene expression and muscle tissue health in the context of collagen 6A3-related congenital myopathy.
        - **Gene Expression:** Represents how much mRNA (messenger RNA) for Collagen 6A3 is present, which influences collagen protein production.
        - **Muscle Health:** Shows the overall health of muscle tissue, ranging from 0 (fully weak) to 1 (fully healthy).
        - **Regeneration:** Simulates the ability of muscle to recover after damage, activated when health drops below maximum.

        Adjust parameters below to explore how gene expression and regeneration rates affect muscle health over time!
        """
    )

with st.sidebar:
    st.markdown(
        "<h2 style='color:#a3c2fd;'>🛠️ Simulation Controls</h2>",
        unsafe_allow_html=True
    )

    transcription_rate = st.slider(
        "Transcription Rate (mRNA production)", 0.0, 5.0, 1.2, 0.05
    )
    degradation_rate = st.slider(
        "Degradation Rate (mRNA loss)", 0.0, 2.0, 0.8, 0.05
    )
    regeneration_rate = st.slider(
        "Regeneration Rate (muscle recovery speed)", 0.0, 1.0, 0.3, 0.01
    )
    simulation_time = st.number_input(
        "Simulation Time (hours)", min_value=1, max_value=120, value=48, step=1
    )
    time_steps = st.number_input(
        "Number of Time Steps", min_value=10, max_value=1000, value=250, step=1
    )

    st.markdown("---", unsafe_allow_html=True)
    st.markdown(
        "<h4 style='color:#ffa500;'>💡 How to use:</h4>"
        "<ul>"
        "<li>Adjust the parameters above and click 'Run Simulation'.</li>"
        "<li>View results, explanations, and download data below.</li>"
        "</ul>", unsafe_allow_html=True
    )
    st.markdown("---", unsafe_allow_html=True)
    st.markdown(
        "<h4 style='color:#ff5050;'>🚀 How to Run Locally:</h4>"
        "<pre>streamlit run app.py</pre>"
        "<h4 style='color:#a3c2fd;'>🌐 How to Deploy:</h4>"
        "Upload this file to Streamlit Cloud or your own server.",
        unsafe_allow_html=True
    )

def simulate_gene_expression(transcription_rate, degradation_rate, simulation_time, time_steps):
    dt = simulation_time / time_steps
    mRNA = np.zeros(time_steps)
    t = np.linspace(0, simulation_time, time_steps)
    mRNA[0] = 1.0
    for i in range(1, time_steps):
        dmRNA = transcription_rate
