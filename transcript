from flask import Flask, request, jsonify
import openai
import ffmpeg
import os

app = Flask(__name__)

# Configure sua API Key da OpenAI
OPENAI_API_KEY = "sua-api-key"
openai.api_key = OPENAI_API_KEY

UPLOAD_FOLDER = "uploads"
os.makedirs(UPLOAD_FOLDER, exist_ok=True)

@app.route('/transcribe', methods=['POST'])
def transcribe_video():
    if 'video' not in request.files:
        return jsonify({"error": "No video file provided"}), 400
    
    video = request.files['video']
    video_path = os.path.join(UPLOAD_FOLDER, video.filename)
    audio_path = video_path.replace(".mp4", ".mp3")

    # Salvar o vídeo
    video.save(video_path)

    # Extrair áudio
    ffmpeg.input(video_path).output(audio_path, format="mp3").run()

    # Transcrição com Whisper
    with open(audio_path, "rb") as audio_file:
        transcript = openai.Audio.transcribe("whisper-1", audio_file)

    # Limpar arquivos temporários
    os.remove(video_path)
    os.remove(audio_path)

    return jsonify({"transcription": transcript["text"]})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
