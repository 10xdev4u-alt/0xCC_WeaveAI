# Voice Agent

The Voice Agent is WEAVE's dedicated specialist for all voice-based interactions. Its primary goal is to make conversation with WEAVE as natural and effortless as speaking to a person. It leverages Gemini's native multimodal capabilities to handle complex audio tasks in a single, efficient step.

## Core Responsibilities

*   **Speech-to-Text (ASR):** Transcribes user voice notes and real-time speech from IVR systems with high accuracy.
*   **Language Identification:** Detects the language and dialect being spoken, including code-mixed languages like Hinglish.
*   **Text-to-Speech (TTS):** Synthesizes natural-sounding voice responses to be played back to the user.

## Triggers

The Voice Agent is activated whenever audio is the primary input modality.
*   User sends a voice note in WhatsApp.
*   User interacts with a voice-activated IVR (Interactive Voice Response) system connected to WEAVE.
*   User clicks the "microphone" icon in the web app for voice input.

## System Prompt

The prompt for this agent is less about personality and more about technical instruction for accurate processing.

```
You are WEAVE's Voice Agent. Your task is to process incoming audio and transcribe it accurately for the Orchestrator.

Your approach:
1.  Listen to the provided audio stream.
2.  Identify the primary language and any code-mixing (e.g., Hindi + English).
3.  Transcribe the speech to text with the highest possible accuracy.
4.  Preserve the original intent and nuance of the spoken words.
5.  If you are asked to generate speech, produce a clear, natural-sounding voice in the specified language.

Your output is a critical input for all other WEAVE agents. Accuracy is paramount.
```

## Authorized Functions

*   `transcribe_audio`: The core function that uses Gemini's ASR.
*   `synthesize_speech`: The core function that uses Gemini's TTS.
*   `detect_language_from_audio`: A specialized function to identify the language before full transcription.

## Example Implementation

The Voice Agent's implementation is a clear demonstration of Gemini's powerful, all-in-one multimodal capabilities. Instead of needing separate APIs for transcription and language ID, Gemini can handle it in a single call.

```python
# agents/voice_agent.py (Illustrative)

from .base_agent import BaseAgent
from ..schemas import UserInput, Context, Response

class VoiceAgent(BaseAgent):
    """
    Handles all voice-based interactions (ASR and TTS).
    """
    
    SYSTEM_PROMPT = "..." # As defined above
    FUNCTIONS = [] # This agent primarily processes data, doesn't call external tools.
    
    def __init__(self):
        super().__init__(
            name="voice",
            system_prompt=self.SYSTEM_PROMPT,
            functions=self.FUNCTIONS
        )

    async def process_voice_input(self, audio_bytes: bytes, context: Context) -> dict:
        """
        Processes a user's voice input and returns the transcription
        and detected language for the Orchestrator to use.
        """
        
        # 1. Build a prompt for Gemini's multimodal model.
        prompt = """
        Analyze the following audio.
        
        Output a JSON object with two keys:
        - "language": The detected language code (e.g., "hi-en" for Hinglish).
        - "transcription": The transcribed text.
        """
        
        # 2. Call Gemini with the audio data.
        response = await self.gemini_client.generate(
            prompt=prompt,
            system_prompt=self.system_prompt,
            audio=audio_bytes
        )
        
        parsed_response = self.gemini_client.parse_json(response.text)
        
        return {
            "language": parsed_response.get("language", "en"),
            "transcription": parsed_response.get("transcription", "")
        }

    async def generate_speech_response(self, text: str, language_code: str) -> bytes:
        """
        Converts text into a spoken response for an IVR or voice assistant.
        """
        # This would use a Gemini TTS model or a dedicated Text-to-Speech API.
        prompt = f"""
        Synthesize the following text into natural-sounding speech
        in the language '{language_code}'.

        Text: "{text}"
        """
        
        # Hypothetical call to a TTS model
        audio_bytes = await self.gemini_client.synthesize_speech(
            prompt=prompt
        )
        
        return audio_bytes

```
By handling complex audio processing natively, the Voice Agent makes voice a first-class citizen in the WEAVE ecosystem, dramatically improving accessibility and user convenience.
