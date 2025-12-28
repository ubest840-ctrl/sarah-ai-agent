# COPY THIS INTO main.py
import os
import requests
from fastapi import FastAPI, Request, HTTPException, Header
from pydantic import BaseModel

app = FastAPI()

# Configuration from Railway Environment Variables
FUB_API_KEY = os.getenv("FUB_API_KEY")
FUB_BASE_URL = "https://api.followupboss.com/v1"
X_SARAH_TOKEN = os.getenv("X_SARAH_TOKEN", "default-secret")

class ToolCallRequest(BaseModel):
    function_name: str
    arguments: dict
    lead_id: str

@app.post("/voice/tool-handler")
async def handle_tool_call(request: ToolCallRequest, x_sarah_token: str = Header(None)):
    if x_sarah_token != X_SARAH_TOKEN:
        raise HTTPException(status_code=401, detail="Unauthorized")

    fn = request.function_name
    args = request.arguments
    lead_id = request.lead_id

    if fn == "update_lead_profile":
        note_content = "Sarah AI Update: " + ", ".join([f"{k}: {v}" for k, v in args.items()])
        requests.post(f"{FUB_BASE_URL}/notes", auth=(FUB_API_KEY, ""), 
                      json={"personId": lead_id, "body": note_content, "subject": "Lead Intel"})
        return {"status": "success", "message": "CRM updated."}

    elif fn == "check_availability":
        # Professional Default: Tuesday/Thursday 10am or 2pm
        return {"status": "available", "options": ["10:00 AM", "2:00 PM"], "message": "Available Tuesday or Thursday."}

    return {"status": "error", "message": "Function not found."}
