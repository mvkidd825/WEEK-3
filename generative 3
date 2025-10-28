
# ============================================================================
# SPIKY GENERATIVE POSTER - STREAMLIT VERSION
# Save this as your main .py file for Streamlit
# ============================================================================

import streamlit as st
import random
import math
import numpy as np
from matplotlib.colors import hsv_to_rgb
import matplotlib.pyplot as plt
import io
from datetime import datetime

st.set_page_config(page_title="Spiky Poster Generator", page_icon="⭐", layout="wide")

# ============================================================================
# FUNCTIONS
# ============================================================================

def spiky_blob(center=(0.5, 0.5), r=0.3, points=200, wobble=0.15, spikes=6, spike_sharpness=0.5):
    """Generate a spiky wobbly shape with pronounced pointed edges."""
    angles = np.linspace(0, 2*math.pi, points, endpoint=False)
    
    # Create spike pattern using sine wave
    spike_pattern = 1 + spike_sharpness * np.sin(spikes * angles + random.random() * 2 * np.pi)
    
    # Add random wobble on top of spikes
    random_wobble = 1 + wobble * (np.random.rand(points) - 0.5)
    
    # Combine spike pattern with wobble
    radii = r * spike_pattern * random_wobble
    
    x = center[0] + radii * np.cos(angles)
    y = center[1] + radii * np.sin(angles)
    return x, y

def make_palette(k=6, mode="random", base_h=0.60):
    """Return k colors (RGB) by sampling HSV depending on mode."""
    cols = []
    for _ in range(k):
        if mode == "pastel":
            h = random.random()
            s = random.uniform(0.15, 0.35)
            v = random.uniform(0.90, 1.00)
        elif mode == "vivid":
            h = random.random()
            s = random.uniform(0.80, 1.00)
            v = random.uniform(0.80, 1.00)
        elif mode == "mono":
            h = base_h
            s = random.uniform(0.20, 0.60)
            v = random.uniform(0.50, 1.00)
        else:
            h = random.random()
            s = random.uniform(0.30, 1.00)
            v = random.uniform(0.50, 1.00)
        cols.append(tuple(hsv_to_rgb([h, s, v])))
    return cols

STYLE_PRESETS = {
    "Minimal": {
        "n_layers": 5,
        "wobble_range": (0.02, 0.08),
        "alpha_range": (0.30, 0.50),
        "palette_mode": "pastel",
        "spike_range": (4, 6),
        "sharpness_range": (0.3, 0.5)
    },
    "Vivid": {
        "n_layers": 12,
        "wobble_range": (0.05, 0.20),
        "alpha_range": (0.35, 0.70),
        "palette_mode": "vivid",
        "spike_range": (5, 8),
        "sharpness_range": (0.4, 0.7)
    },
    "NoiseTouch": {
        "n_layers": 14,
        "wobble_range": (0.12, 0.30),
        "alpha_range": (0.25, 0.55),
        "palette_mode": "mono",
        "spike_range": (6, 10),
        "sharpness_range": (0.5, 0.8)
    },
    "Crystal": {
        "n_layers": 10,
        "wobble_range": (0.03, 0.10),
        "alpha_range": (0.40, 0.65),
        "palette_mode": "vivid",
        "spike_range": (7, 12),
        "sharpness_range": (0.6, 0.9)
    },
    "Custom": None  # For manual control
}

def generate_poster(style=None, seed=None,
                    n_layers=8,
                    radius_range=(0.15, 0.45),
                    wobble_range=(0.05, 0.25),
                    alpha_range=(0.25, 0.6),
                    spike_range=(5, 8),
                    sharpness_range=(0.4, 0.7),
                    figsize=(7, 10),
                    background=(0.98, 0.98, 0.97),
                    palette_mode="pastel"):
    """Create a spiky generative poster."""
    
    # Apply style preset if provided
    if style and style in STYLE_PRESETS and STYLE_PRESETS[style] is not None:
        preset = STYLE_PRESETS[style]
        n_layers = preset["n_layers"]
        wobble_range = preset["wobble_range"]
        alpha_range = preset["alpha_range"]
        palette_mode = preset["palette_mode"]
        spike_range = preset["spike_range"]
        sharpness_range = preset["sharpness_range"]
    
    # Reproducibility
    if seed is not None:
        random.seed(seed)
        np.random.seed(seed)
    
    # Canvas
    fig, ax = plt.subplots(figsize=figsize)
    ax.axis('off')
    ax.set_facecolor(background)
    
    # Colors
    palette = make_palette(6, mode=palette_mode)
    
    # Generate layers
    for _ in range(n_layers):
        cx, cy = random.random(), random.random()
        rr = random.uniform(*radius_range)
        wob = random.uniform(*wobble_range)
        spk = random.randint(*spike_range)
        shp = random.uniform(*sharpness_range)
        
        x, y = spiky_blob(center=(cx, cy), r=rr, wobble=wob,
                         spikes=spk, spike_sharpness=shp)
        
        color = random.choice(palette)
        alpha = random.uniform(*alpha_range)
        ax.fill(x, y, color=color, alpha=alpha, edgecolor=(0, 0, 0, 0))
    
    # Title
    ax.text(0.05, 0.95, f"Generative Poster • {style or 'Custom'} (Spiky)",
            fontsize=14, weight='bold', transform=ax.transAxes)
    
    ax.set_xlim(0, 1)
    ax.set_ylim(0, 1)
    
    return fig

# ============================================================================
# STREAMLIT UI
# ============================================================================

st.title("⭐ Spiky Generative Poster Studio")
st.markdown("Create abstract art with spiky, star-like shapes")

# Sidebar
st.sidebar.header("🎛️ Controls")

# Style preset selector
style_choice = st.sidebar.selectbox(
    "Choose Style Preset",
    list(STYLE_PRESETS.keys()),
    index=0
)

st.sidebar.markdown("---")

# Seed control
col1, col2 = st.sidebar.columns([3, 1])
with col1:
    seed = st.number_input("Seed", min_value=0, max_value=9999, value=42, step=1)
with col2:
    if st.button("🎲"):
        seed = random.randint(0, 9999)
        st.rerun()

# Show preset info or custom controls
if style_choice == "Custom":
    st.sidebar.markdown("### Custom Parameters")
    n_layers = st.sidebar.slider("Layers", 3, 25, 8)
    
    st.sidebar.markdown("**Wobble Range**")
    wobble_min = st.sidebar.slider("Wobble Min", 0.01, 0.3, 0.05, 0.01)
    wobble_max = st.sidebar.slider("Wobble Max", 0.01, 0.4, 0.25, 0.01)
    wobble_range = (wobble_min, wobble_max)
    
    st.sidebar.markdown("**Spike Range**")
    spike_min = st.sidebar.slider("Spike Min", 3, 10, 5)
    spike_max = st.sidebar.slider("Spike Max", 4, 12, 8)
    spike_range = (spike_min, spike_max)
    
    st.sidebar.markdown("**Sharpness Range**")
    sharp_min = st.sidebar.slider("Sharpness Min", 0.1, 0.8, 0.4, 0.05)
    sharp_max = st.sidebar.slider("Sharpness Max", 0.2, 1.0, 0.7, 0.05)
    sharpness_range = (sharp_min, sharp_max)
    
    palette_mode = st.sidebar.selectbox("Palette", ["pastel", "vivid", "mono", "random"])
    
    alpha_min = st.sidebar.slider("Alpha Min", 0.1, 0.6, 0.25, 0.05)
    alpha_max = st.sidebar.slider("Alpha Max", 0.3, 0.8, 0.6, 0.05)
    alpha_range = (alpha_min, alpha_max)
    
    # Generate with custom params
    fig = generate_poster(
        style=None,
        seed=seed,
        n_layers=n_layers,
        wobble_range=wobble_range,
        alpha_range=alpha_range,
        spike_range=spike_range,
        sharpness_range=sharpness_range,
        palette_mode=palette_mode
    )
else:
    # Show preset description
    preset_info = {
        "Minimal": "🌸 Soft, gentle spikes with pastel colors (5 layers)",
        "Vivid": "🎆 Bright, bold colors with medium spikes (12 layers)",
        "NoiseTouch": "🌪️ High chaos with many sharp spikes (14 layers)",
        "Crystal": "💎 Ultra-sharp crystalline forms (10 layers)"
    }
    
    if style_choice in preset_info:
        st.sidebar.info(preset_info[style_choice])
    
    # Generate with preset
    fig = generate_poster(style=style_choice, seed=seed)

# Display poster
st.pyplot(fig)

# Download button
buf = io.BytesIO()
fig.savefig(buf, format='png', dpi=300, bbox_inches='tight', pad_inches=0.1)
buf.seek(0)

st.download_button(
    label="⬇️ Download Poster (High Resolution)",
    data=buf,
    file_name=f"spiky_poster_{style_choice}_{seed}_{datetime.now().strftime('%Y%m%d_%H%M%S')}.png",
    mime="image/png"
)

plt.close(fig)

# Info section
with st.expander("ℹ️ Style Presets Info"):
    st.markdown("""
    ### 🎨 Available Presets:
    
    **Minimal** 🌸
    - 5 layers, soft spikes (4-6 points)
    - Pastel palette, low wobble
    - Perfect for: Gentle, calming designs
    
    **Vivid** 🎆
    - 12 layers, medium spikes (5-8 points)
    - Bright saturated colors
    - Perfect for: Bold, energetic posters
    
    **NoiseTouch** 🌪️
    - 14 layers, many spikes (6-10 points)
    - Monochromatic, high wobble
    - Perfect for: Chaotic, textured art
    
    **Crystal** 💎
    - 10 layers, ultra-sharp spikes (7-12 points)
    - Vivid colors, minimal wobble
    - Perfect for: Geometric, crystalline forms
    
    **Custom** 🛠️
    - Full manual control over all parameters
    - Create your unique style!
    """)

with st.expander("📚 How to Use"):
    st.markdown("""
    1. **Choose a preset** or select "Custom" for full control
    2. **Adjust the seed** to get different compositions (same seed = same result)
    3. **Click the dice 🎲** for a random seed
    4. **Download** your favorite designs in high resolution
    
    **Tips:**
    - Try seeds: 42, 99, 777, 2024 for interesting patterns
    - Mix presets by changing seeds
    - Custom mode lets you fine-tune every parameter
    """)
