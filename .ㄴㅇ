import streamlit as st
import google.generativeai as genai

# 페이지 설정
st.set_page_config(page_title="AI 여행 가이드", page_icon="✈️", layout="centered")

st.title("✈️ AI 여행지 추천 챗봇")
st.caption("여행 취향, 예산, 일정을 알려주시면 딱 맞는 여행지를 추천해 드립니다!")

# 1. API 키 설정 (Streamlit Secrets에서 불러오기)
try:
    API_KEY = st.secrets["GEMINI_API_KEY"]
    genai.configure(api_key=API_KEY)
except KeyError:
    st.error("⚠️ Streamlit Secrets에 `GEMINI_API_KEY`가 설정되지 않았습니다. 앱 설정에서 API 키를 추가해 주세요.")
    st.stop()

# 2. 모델 및 채팅 세션 초기화 (상태 유지)
if "chat_session" not in st.session_state:
    # 여행 가이드 역할을 부여하기 위해 system_instruction 사용
    model = genai.GenerativeModel(
        model_name="gemini-2.5-flash-lite",
        system_instruction="당신은 친절하고 전문적인 전 세계 여행지 추천 가이드입니다. 사용자의 취향, 예산, 일정에 맞는 여행지를 구체적인 이유와 함께 추천해주세요."
    )
    # 채팅 기록을 빈 리스트로 시작
    st.session_state.chat_session = model.start_chat(history=[])

# 3. 기존 채팅 기록 화면에 렌더링
for message in st.session_state.chat_session.history:
    # Gemini API의 역할 이름('model', 'user')을 Streamlit 역할 이름('assistant', 'user')으로 변환
    role = "assistant" if message.role == "model" else "user"
    with st.chat_message(role):
        st.markdown(message.parts[0].text)

# 4. 사용자 입력 및 챗봇 응답 처리
if prompt := st.chat_input("어떤 여행지를 찾고 계신가요? (예: 100만원으로 갈 수 있는 3박 4일 동남아 휴양지)"):
    
    # 사용자의 메시지를 화면에 출력
    with st.chat_message("user"):
        st.markdown(prompt)

    # 챗봇(모델)의 응답을 화면에 출력하고 기록에 추가
    with st.chat_message("assistant"):
        try:
            with st.spinner("여행지를 찾고 있습니다..."):
                # send_message를 사용하면 자동으로 history에 대화가 누적됩니다.
                response = st.session_state.chat_session.send_message(prompt)
                st.markdown(response.text)
        except Exception as e:
            # 오류 처리 (할당량 초과, 네트워크 오류 등)
            st.error(f"오류가 발생했습니다. 잠시 후 다시 시도해주세요.\n\n상세 내용: {e}")
