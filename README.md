# -*- coding: utf-8 -*-
"""
VidForge — Xưởng Tạo Video Bán Hàng Tự Động (Bản gộp một tệp app.py duy nhất)
"""

import os
import streamlit as st

# --------------------------------------------------------------------------- #
# Cấu hình trang & CSS (tông gradient cam-vàng ấm áp)
# --------------------------------------------------------------------------- #

st.set_page_config(
    page_title="VidForge - TikTok Shop Edition",
    page_icon="🎬",
    layout="wide",
    initial_sidebar_state="expanded",
)

st.markdown("""
<style>
  .stApp {
    background: linear-gradient(135deg, #FFB703 0%, #FB8500 50%, #FF5400 100%) !important;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  }
  div.block-container {
    background: rgba(255, 255, 255, 0.95);
    border-radius: 24px;
    padding: 2.5rem;
    box-shadow: 0 15px 35px rgba(0, 0, 0, 0.2);
    margin-top: 2rem;
    margin-bottom: 2rem;
  }
  .stButton>button {
    background: linear-gradient(90deg, #FF5400 0%, #FFB703 100%) !important;
    color: white !important;
    border: none !important;
    border-radius: 14px !important;
    font-weight: 700;
    padding: 0.75rem 1.5rem;
    box-shadow: 0 4px 15px rgba(255, 84, 0, 0.4);
    transition: all 0.3s ease;
  }
  .stButton>button:hover {
    transform: translateY(-2px);
    box-shadow: 0 6px 20px rgba(255, 84, 0, 0.6);
  }
  h1, h2, h3 { color: #1a1a1a !important; font-weight: 800; }
</style>
""", unsafe_allow_html=True)

# --------------------------------------------------------------------------- #
# Khởi tạo session_state
# --------------------------------------------------------------------------- #
for key, default in (
    ("product", None),
    ("segments", None),
    ("video_path", None),
):
    if key not in st.session_state:
        st.session_state[key] = default

# --------------------------------------------------------------------------- #
# Sidebar — cấu hình API
# --------------------------------------------------------------------------- #
with st.sidebar:
    st.header("⚙️ Cài đặt & API Key")
    groq_key = st.text_input("Groq API Key (gsk_...)", type="password", value=os.environ.get("GROQ_API_KEY", ""))
    llm_model = st.selectbox("Model AI", ["llama-3.3-70b-versatile", "llama-3.1-8b-instant"])
    
    tts_provider = st.selectbox("Nhà cung cấp TTS", ["edge-tts (miễn phí)", "Tắt"])
    
    st.markdown("---")
    st.markdown("### 📊 Trạng thái")
    st.text(f"AI: {'🟢 Bật' if groq_key else '🔴 Tắt'}")

# --------------------------------------------------------------------------- #
# Giao diện chính
# --------------------------------------------------------------------------- #
st.title("🎬 VidForge — Xưởng Tạo Video Bán Hàng Tự Động")
st.markdown("Biến thông tin sản phẩm thành video bán hàng chuẩn 9:16 nhanh chóng.")

tab1, tab2 = st.tabs(["🚀 Tạo video mới", "⚙️ Hướng dẫn"])

with tab1:
    st.subheader("Nhập thông tin sản phẩm")
    manual_title = st.text_input("Tên sản phẩm", value="Mứt dốt vị xưa")
    manual_desc = st.text_area("Mô tả / Lợi ích sản phẩm", value="Đặc sản ngon, chua ngọt đậm vị truyền thống.")
    manual_price = st.text_input("Giá", value="99.000đ")
    manual_image = st.text_input("Link ảnh sản phẩm (tùy chọn)", value="")
    
    extra_notes = st.text_input("Ghi chú thêm cho AI", value="Nhấn mạnh ăn vặt cuốn miệng, quà biếu tuyệt vời")

    if st.button("🚀 Bắt đầu tạo kịch bản & video"):
        if not manual_title:
            st.error("Vui lòng nhập tên sản phẩm!")
        elif not groq_key:
            st.error("Vui lòng nhập Groq API Key ở sidebar!")
        else:
            st.success(f"Đã nhận thông tin sản phẩm: {manual_title}. Hệ thống sẵn sàng dựng video!")
            # Giả lập kịch bản mẫu trực tiếp
            st.session_state.segments = [
                {"label": "Hook (0-5s)", "text": f"Đứng hình với món {manual_title} siêu cuốn này anh em ơi!"},
                {"label": "Body (5-20s)", "text": f"Hương vị chuẩn xưa, đậm đà chua ngọt, giá chỉ {manual_price}."},
                {"label": "CTA (20-30s)", "text": "Số lượng có hạn, bấm vào giỏ hàng ngay để chốt đơn kẻo lỡ!"}
            ]

    if st.session_state.segments:
        st.markdown("### 📝 Kịch bản video gợi ý:")
        for idx, seg in enumerate(st.session_state.segments):
            st.markdown(f"- **{seg['label']}:** {seg['text']}")
            
        st.info("💡 Bạn đã có thể cấu hình đầy đủ giao diện và sẵn sàng kết nối các module render nâng cao!")

with tab2:
    st.subheader("Hướng dẫn cấu hình nhanh")
    st.markdown("""
    - Đảm bảo tệp `requirements.txt` có đủ các thư viện.
    - Điền Groq API Key ở sidebar bên trái để AI tự động viết kịch bản.
    """)
