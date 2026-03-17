
# ☀️ Agentic AI Weather Assistant: Building Blocks with Amazon Bedrock

A beginner-friendly (Level 100) hands-on workshop demonstrating how to build **Agentic AI from scratch** using pure Python and AWS SDK—no complex frameworks required. In just one hour, learn what makes AI "agentic" by creating an intelligent weather assistant that thinks, plans, acts, and responds autonomously.

> **Workshop Type:** Hands-on, code-from-scratch approach
> **Duration:** ~1 hour
> **Target Audience:** Beginners to AI and AWS
> **Prerequisites:** Basic Python knowledge, AWS account
> **Region:** US West (Oregon) — `us-west-2`

---

## 🎯 What I Built

An AI agent using **pure Python and AWS SDK** that:

| Capability | Implementation |
|------------|----------------|
| **🧠 Thinks** | Interfaces with Claude 4.5 Sonnet through Amazon Bedrock API |
| **🔧 Acts** | Makes HTTP requests to National Weather Service API using Python's subprocess |
| **📊 Processes** | Handles JSON data with native Python data structures |
| **💬 Responds** | Communicates via command-line and Streamlit web interfaces |

---

## 🌟 Key Features

### **Intelligent Location Handling**
The agent accepts multiple location formats:
- City names: "Seattle", "New York City"
- ZIP codes: "90210", "10001"
- Coordinates: "47.6062,-122.3321"
- Descriptions: "National park near Homestead in Florida"
- Reasoning queries: "Largest city in California"

### **Two-Step API Orchestration**
1. **Points API** → Converts location to NWS forecast office coordinates
2. **Forecast API** → Retrieves detailed weather data for that grid

### **Dual Interface**
- **Command-Line Interface** (`weather_agent_cli.py`) — Quick terminal-based queries
- **Web Application** (`weather_agent_web.py`) — Interactive Streamlit UI with real-time progress tracking

---

## 🏗️ Architecture

```
┌─────────────┐
│ User Input  │ → Location (city, ZIP, coordinates, descriptions)
└──────┬──────┘
       ↓
┌─────────────────────┐
│ 🧠 Claude 4.5 Sonnet│ → AI analyzes location and plans API calls
│  (Amazon Bedrock)   │
└──────┬──────────────┘
       ↓
┌────────────────────────────────────┐
│ 📍 Points API URL Generated        │
│ https://api.weather.gov/points/... │
└──────┬─────────────────────────────┘
       ↓
┌─────────────────────┐
│ 🌐 Execute Points   │ → curl command to NWS
│    API Call         │
└──────┬──────────────┘
       ↓
┌─────────────────────┐
│ 📋 Points Response  │ → Extract forecast URL from JSON
└──────┬──────────────┘
       ↓
┌─────────────────────┐
│ 🌤️ Execute Forecast│ → curl to gridpoints/SEW/124,67/forecast
│    API Call         │
└──────┬──────────────┘
       ↓
┌─────────────────────┐
│ 📊 Raw JSON Data    │ → Temperature, wind, conditions
└──────┬──────────────┘
       ↓
┌─────────────────────┐
│ 🧠 Claude Processes │ → Convert raw data to summary
│  (Amazon Bedrock)   │
└──────┬──────────────┘
       ↓
┌─────────────────────┐
│ 💬 Human-Readable   │ → "Today: Partly cloudy, 72°F"
│    Response         │    "Wind: West 5-10 mph"
└─────────────────────┘
```

---

## 🔑 What Makes This "Agentic"?

| Characteristic | Implementation |
|----------------|----------------|
| **🤖 Autonomy** | • Interprets location descriptions<br>• Generates correct API URLs<br>• Decides relevant weather info<br>• Handles errors independently |
| **⚡ Reactivity** | • Adapts to city names, ZIP codes, coordinates<br>• Processes varying API structures<br>• Handles network issues<br>• Works with incomplete data |
| **🎯 Proactivity** | • Plans complete API strategies<br>• Takes initiative to call APIs<br>• Analyzes weather insights<br>• Presents user-friendly results |

---

## 💻 Code Structure

### **Core Functions**

#### **1. Amazon Bedrock Connection**
```python
def call_claude_sonnet(prompt):
    """Connect to Claude 4.5 Sonnet - the agent's 'brain'"""
    bedrock = boto3.client(
        service_name='bedrock-runtime',
        region_name='us-west-2'
    )
    
    response = bedrock.converse(
        modelId='us.anthropic.claude-sonnet-4-5-20250929-v1:0',
        messages=[{
            "role": "user",
            "content": [{"text": prompt}]
        }],
        inferenceConfig={
            "maxTokens": 2000,
            "temperature": 0.7
        }
    )
    
    return True, response['output']['message']['content'][0]['text']
```

#### **2. API Execution**
```python
def execute_curl_command(url):
    """Agent's 'hands' - executes HTTP requests"""
    result = subprocess.run(
        ['curl', '-s', url],
        capture_output=True,
        text=True,
        timeout=30
    )
    return True, result.stdout
```

#### **3. AI Planning**
```python
def generate_weather_api_calls(location):
    """Agent's 'planning brain' - figures out right API calls"""
    prompt = f"""
    You are an expert at working with the National Weather Service API.
    
    Generate the NWS Points API URL for "{location}".
    
    Instructions:
    1. Determine approximate lat/lon coordinates
    2. Generate: https://api.weather.gov/points/{{lat}},{{lon}}
    
    Return ONLY the complete Points API URL.
    """
    
    success, response = call_claude_sonnet(prompt)
    api_url = response.strip()
    return True, [api_url]
```

#### **4. Data Processing**
```python
def process_weather_response(raw_json, location):
    """Agent's 'analysis brain' - makes sense of complex data"""
    prompt = f"""
    Convert this raw NWS forecast data for "{location}" into a 
    clear, helpful weather summary.
    
    Raw Data: {raw_json}
    
    Include: location intro, current conditions, 2-3 day outlook,
    notable patterns/alerts. Format for easy reading.
    """
    
    return call_claude_sonnet(prompt)
```

---

## 🚀 Getting Started

### **Prerequisites**
- Python 3.7+
- AWS account with Bedrock access
- AWS CLI configured

### **Setup**

1. **Clone or download the project files**

2. **Create virtual environment:**
```bash
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. **Install dependencies:**
```bash
pip install boto3 streamlit requests Pillow
```

4. **Configure AWS credentials:**
```bash
aws configure
# Enter your AWS Access Key ID
# Enter your AWS Secret Access Key
# Default region: us-west-2
```

5. **Enable Claude 4.5 Sonnet in Bedrock:**
   - Open Amazon Bedrock console
   - Navigate to Model access
   - Enable `Claude 4.5 Sonnet`
   - Wait for "Access granted" status

### **Running the Agent**

#### **Command-Line Interface:**
```bash
python weather_agent_cli.py
```

#### **Web Application:**
```bash
streamlit run weather_agent_web.py
```
Access at: `http://localhost:8501`

---

## 🧪 Test Cases

Try these example queries:
- "Seattle" (major city)
- "90210" (ZIP code)
- "New York City" (multi-word city)
- "National park near Homestead in Florida" (descriptive)
- "Largest City in California" (requires reasoning)

---

## 🛠️ Technologies Used

### **AWS Services**
- **Amazon Bedrock** — Foundation model hosting (Claude 4.5 Sonnet)
- **AWS IAM** — Authentication and permissions
- **boto3** — AWS SDK for Python

### **AI Models**
- **Claude 4.5 Sonnet** (`us.anthropic.claude-sonnet-4-5-20250929-v1:0`)
  - Complex reasoning and planning
  - Processing structured data
  - Human-like responses

### **APIs & Data Sources**
- **National Weather Service API** — Free, real-time US weather data
  - Points API: Location to forecast office mapping
  - Forecast API: Detailed weather forecasts

### **Development Tools**
- **Python 3.7+** — Core programming language
- **Streamlit** — Web interface framework
- **subprocess** — HTTP requests via curl
- **json** — Data processing

---

## 📸 Screenshots

The project includes screenshots of:

### **Web Application Interface**
1. **Main Landing Page**
   - Dark-themed UI with location input field
   - "Get Weather Forecast" button
   - "Clear Results" button
   - Example suggestions section

2. **Weather Analysis Process**
   - 6-step process visualization with checkmarks
   - Generated Points API URL display
   - Expandable raw API responses
   - Process status sidebar
   - "What Makes This Agentic?" explanation

3. **Final Weather Forecast Display**
   - Location and timestamp
   - Current conditions
   - 3-day detailed outlook with temperatures and precipitation chances

All screenshots are stored in the `/images` folder.

---

## 🌍 Real-World Applications

The agentic patterns learned in this project apply to:

### **Weather & Environmental**
- Forecast assistants with alerts
- Climate monitoring and predictions
- Disaster response coordination

### **Travel & Transportation**
- Trip planning with weather integration
- Route optimization
- Event management

### **Business & Finance**
- Market analysis with data gathering
- Customer support with knowledge bases
- Risk assessment

### **Software Development**
- Code assistants
- DevOps automation
- Documentation generation

---

## 🎓 Learning Outcomes

By completing this workshop, I gained expertise in:

✅ **Amazon Bedrock Integration** — Connecting to Claude 4.5 Sonnet and handling API responses
✅ **API Integration Patterns** — AI-driven URL generation and sequential API calls
✅ **Prompt Engineering** — Structured prompts for specific tasks and output formatting
✅ **Agentic Workflow Design** — Think → Plan → Act → Process → Respond pattern
✅ **JSON Data Processing** — Extracting and transforming complex API responses
✅ **Error Handling** — Building resilient systems with proper exception management
✅ **UI Development** — Creating both CLI and web interfaces with Streamlit

---

## 🚀 Next Steps

### **Immediate Extensions**
- Add more APIs (international weather, air quality, traffic)
- Improve error handling with retry logic
- Add memory to store previous queries
- Enhance UI with weather maps and charts

### **Intermediate Projects**
- Multi-agent systems (weather + travel + events)
- Tool integration (calculators, databases, notifications)
- Conversation flow with context maintenance

### **Advanced Concepts**
- Production deployment (Lambda, ECS, EKS)
- Security & compliance (auth, encryption, audits)
- Performance optimization (caching, parallelization)

---

## 💡 Key Takeaway

**The Agentic AI Workflow:**
```
Input → AI Planning → Action → AI Processing → Response
```

This pattern is **reusable across countless domains**—from customer support to research automation to business intelligence. Anyone can build intelligent systems that reason, plan, and take meaningful actions in the real world.

---

## 👤 Workshop Contributor

**Ramakrishna Natarajan**
Sr. Partner Solutions Architect, AWS

---

## 👤 Author

**Ahmad Sultani**
🔗 [LinkedIn](https://linkedin.com/in/asultani91) | 🐦 [X (Twitter)](https://x.com/as_sultani)
☁️ AWS Solutions Architect Associate (SAA-C03) — In Progress
🤖 Specialization: Generative AI & Agentic Systems
📺 [Ava Tech YouTube Channel](https://youtube.com/@AvatechChannel) — AWS Tutorials & Tech Updates

---

## 📄 License

MIT.

---

**Welcome to the world of agentic AI!** 🚀
