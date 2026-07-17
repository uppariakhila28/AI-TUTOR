# AI TUTOR
from google import genai
import gradio as gr

# ==============================
# Gemini API Key
# ==============================
API_KEY = "YOUR_GEMINI_API_KEY"

# Create client
client = genai.Client(api_key=API_KEY)

# ==============================
# System Prompt
# ==============================
SYSTEM_PROMPT = """
You are StudyMate AI, a highly intelligent tutor and educational assistant.

Rules:
- Answer study-related questions clearly.
- Explain concepts in simple language.
- Give step-by-step explanations.
- Use examples whenever possible.
- If coding is requested, provide complete code with comments.
- Show calculations for numerical problems.
- Use headings and bullet points.
- Be polite and professional.
"""

# Create chat session
chat = client.chats.create(
    model="gemini-2.5-flash",
    config={
        "system_instruction": SYSTEM_PROMPT
    }
)


# ==============================
# Chat Function
# ==============================
def respond(message, history):
    global chat

    try:
        response = chat.send_message(message)
        return response.text

    except Exception as e:
        return f"❌ Error:\n\n{e}"


# ==============================
# Clear Chat
# ==============================
def clear_chat():
    global chat

    chat = client.chats.create(
        model="gemini-2.5-flash",
        config={
            "system_instruction": SYSTEM_PROMPT
        }
    )

    return []


# ==============================
# UI
# ==============================
with gr.Blocks(title="StudyMate AI") as demo:

    gr.Markdown("# 📚 StudyMate AI")
    gr.Markdown("Ask any study-related question.")

    chatbot = gr.Chatbot(height=500)

    msg = gr.Textbox(
        placeholder="Ask your question...",
        label="Question"
    )

    clear = gr.Button("🗑 Clear Chat")

    def chat_fn(message, history):
        answer = respond(message, history)
        history.append((message, answer))
        return history, ""

    msg.submit(
        chat_fn,
        inputs=[msg, chatbot],
        outputs=[chatbot, msg]
    )

    clear.click(
        fn=clear_chat,
        outputs=chatbot
    )

demo.launch(share=True)
