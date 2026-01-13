 Launch venv with this command
   .\venv\Scripts\Activate.ps1
   
taskkill /PID 12056 /F

C:\Users\Sean Fitz\Desktop\nextwork-rag-api
"C:\Users\Sean Fitz\Desktop\nextwork-rag-api"

PS C:\Users\Sean Fitz> cd "C:\Users\Sean Fitz\Desktop\nextwork-rag-api"
PS C:\Users\Sean Fitz\Desktop\nextwork-rag-api> .\venv\Scripts\Activate.ps1
(venv) PS C:\Users\Sean Fitz\Desktop\nextwork-rag-api>

```powershell
cd ~\Desktop\nextwork-rag-api
venv\Scripts\activate

```

Make sure these are running first. Do this first

Start environment
cd ~\Desktop\nextwork-rag-api
venv\Scripts\activate


Terminal 1
ollama serve

Terminal 2
uvicorn app:app --reload

Terminal 3
<!-- command 1 -->
cd ~\Desktop\nextwork-rag-api
venv\Scripts\activate

<!-- command 2 -->
Invoke-WebRequest -Uri "http://127.0.0.1:8000/query?q=What%20is%20Kubernetes%3F" -Method POST

