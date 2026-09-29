from fastapi import FastAPI
from pydantic import BaseModel
import google.generativeai as genai
import os

# Unga Gemini API Key
genai.configure(api_key="YOUR_GEMINI_API_KEY_HERE")

app = FastAPI(title="ComicCraft - AI Augmented Backend")

class ComicRequest(BaseModel):
    prompt: str
    genre: str = "adventure"
    pages: int = 4

@app.get("/")
def home():
    return {"message": "ComicCraft Backend is Running 🚀", "creator": "Raji.m"}

@app.post("/generate-comic")
def generate_comic(req: ComicRequest):
    model = genai.GenerativeModel('gemini-1.5-flash')
    
    full_prompt = f"""
    You are ComicCraft, an AI Comic Creator.
    Create a comic book story based on:
    Idea: {req.prompt}
    Genre: {req.genre}
    Pages: {req.pages}

    Output format must be JSON like this:
    {{
      "title": "Comic Title",
      "pages": [
        {{"page": 1, "scene": "description", "dialogue": "character dialogues", "image_prompt": "prompt for AI image"}},
        ...
      ]
    }}
    Make it fun, visual, and engaging.
    """
    
    response = model.generate_content(full_prompt)
    return {"status": "success", "comic": response.text}

# Run command: uvicorn main:app --reload
