<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SGT-A35 Bulk Strip Notes</title>
    <style>
        body { font-family: -apple-system, sans-serif; background-color: #f0f2f5; padding: 20px; line-height: 1.6; }
        .container { max-width: 650px; margin: auto; background: white; padding: 30px; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); }
        h1 { color: #1a73e8; text-align: center; margin-bottom: 30px; border-bottom: 2px solid #f0f2f5; padding-bottom: 10px; }
        h2 { color: #5f6368; font-size: 1.1rem; text-transform: uppercase; margin-top: 35px; border-left: 4px solid #1a73e8; padding-left: 10px; }
        
        .input-group { margin-bottom: 15px; padding: 12px; background: #fafafa; border-radius: 6px; border: 1px solid #eee; }
        .comment-group { margin-bottom: 25px; padding: 12px; background: #fffdf0; border-radius: 6px; border: 1px dashed #d1cfbb; }
        
        label { display: block; font-weight: 600; margin-bottom: 5px; font-size: 0.95rem; }
        
        input[type="email"], select, textarea { 
            width: 100%; padding: 10px; border-radius: 4px; border: 1px solid #dadce0; font-size: 1rem; box-sizing: border-box;
        }
        
        textarea { resize: vertical; min-height: 80px; font-family: inherit; }

        button { 
            width: 100%; padding: 18px; background-color: #1a73e8; color: white; 
            border: none; border-radius: 8px; font-size: 1.1rem; cursor: pointer; font-weight: bold; margin-top: 30px;
        }
        button:hover { background-color: #1765cc; }
    </style>
</head>
<body>

<div class="container">
    <h1>SGT-A35 (RB211) Bulk Strip Notes</h1>
    
    <div class="input-group" style="background: #e8f0fe; border: 1px solid #1a73e8;">
        <label for="recipient-email">Recipient Email Address</label>
        <input type="email" id="recipient-email" placeholder="inspector@company.com" required>
    </div>

    <div id="survey-sections">
        </div>

    <button onclick="sendEmail()">Finalize & Send Report</button>
</div>

<script>
    const reportData = [
        {
            module: "01 Module (Air Intake Casing)",
            questions: [
                { label: "IGV Bushing Wear", options: ["None", "Minor", "Excessive"] },
                { label: "Stage 1 Blade Leading Edge", options: ["Clean", "Erosion", "FOD"] }
            ]
        },
        {
            module: "02 Module (IP Compressor)",
            questions: [
                { label: "Casing Internal Coating", options: ["Intact", "Peeling", "Scored"] },
                { label: "Stator Vane Condition", options: ["Serviceable", "Dented", "Replace"] }
            ]
        },
        {
            module: "03 Module (Intermediate Casing)",
            questions: [
                { label: "Fuel Nozzle Carbonizing", options: ["None", "Light", "Heavy"] },
                { label: "Liner Cooling Holes", options: ["Clear", "Partially Blocked", "Burned"] }
            ]
        },
        {
            module: "04 Module (High Pressure Section)",
            questions: [
                { label: "Stage 1 Nozzle Cracking", options: ["None", "Within Limits", "OOT"] }
            ]
        },
        {
            module: "05 Module (IP Turbine)",
            questions: [
                { label: "LPT Blade Tip Curl", options: ["None", "Minor", "Severe"] }
            ]
        },
        {
            module: "06 Module (Non-modular Components)",
            questions: [
                { label: "Gear Teeth Contact Pattern", options: ["Normal", "Pitting", "Scuffing"] }
            ]
        },
        {
            module: "Misc",
            questions: [
                { label: "Magnetic Chip Detector", options: ["Clean", "Fuzz", "Metallic Flakes"] }
            ]
        }
    ];

    const sectionContainer = document.getElementById('survey-sections');
    
    reportData.forEach((sec, sIdx) => {
        // Section Header
        const header = document.createElement('h2');
        header.innerText = sec.module;
        sectionContainer.appendChild(header);

        // Questions
        sec.questions.forEach((q, qIdx) => {
            const div = document.createElement('div');
            div.className = 'input-group';
            div.innerHTML = `
                <label>${q.label}</label>
                <select id="s-${sIdx}-q-${qIdx}">
                    ${q.options.map(opt => `<option value="${opt}">${opt}</option>`).join('')}
                </select>
            `;
            sectionContainer.appendChild(div);
        });

        // Additional Comments Textbox for this Section
        const commentDiv = document.createElement('div');
        commentDiv.className = 'comment-group';
        commentDiv.innerHTML = `
            <label>Additional Comments (${sec.module})</label>
            <textarea id="comment-${sIdx}" placeholder="Enter measurements, serial numbers, or specific observations..."></textarea>
        `;
        sectionContainer.appendChild(commentDiv);
    });

    function sendEmail() {
        const recipient = document.getElementById('recipient-email').value;
        if (!recipient) { alert("Please enter a recipient email."); return; }

        const subject = "Gas Turbine Condition Report";
        let body = "GAS TURBINE CONDITION REPORT\n============================\n\n";

        reportData.forEach((sec, sIdx) => {
            body += `[${sec.module}]\n`;
            
            // Collect question answers
            sec.questions.forEach((q, qIdx) => {
                const answer = document.getElementById(`s-${sIdx}-q-${qIdx}`).value;
                body += `- ${q.label}: ${answer}\n`;
            });

            // Collect comments
            const comment = document.getElementById(`comment-${sIdx}`).value;
            if (comment.trim() !== "") {
                body += `NOTES: ${comment}\n`;
            }
            
            body += "\n";
        });

        const mailtoLink = `mailto:${recipient}?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(body)}`;
        window.location.href = mailtoLink;
    }
</script>

</body>
</html>
