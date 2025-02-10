document.addEventListener("DOMContentLoaded", () => {
    const form = document.getElementById("participant-form");
    const experimentDiv = document.getElementById("experiment");
    const resultDisplay = document.getElementById("result");
    
    let startTime;
    let timeResults = [];
    
    const timeButtons = [5, 5, 5, 10, 30, 60, 120, 150].map((interval) => {
        const button = document.createElement("button");
        button.innerText = `${interval} sec`;
        button.dataset.interval = interval;
        button.disabled = false;
        experimentDiv.appendChild(button);
        return button;
    });
    
    timeButtons.forEach(button => {
        button.addEventListener("click", () => {
            if (!startTime) {
                startTime = Date.now();
                button.innerText = "Stop";
            } else {
                const endTime = Date.now();
                const elapsedTime = (endTime - startTime) / 1000;
                timeResults.push({ interval: button.dataset.interval, elapsed: elapsedTime });
                resultDisplay.innerText = `Elapsed Time: ${elapsedTime.toFixed(2)} seconds for ${button.dataset.interval} sec`;
                startTime = null;
                button.innerText = `${button.dataset.interval} sec`;
            }
        });
    });
    
    form.addEventListener("submit", async (event) => {
        event.preventDefault();
        experimentDiv.style.display = "block";
        form.style.display = "none";
        
        const age = document.getElementById("age").value;
        const gender = document.getElementById("gender").value;
        const occupation = document.getElementById("occupation").value;
        
        const response = await fetch("/submit", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ age, gender, occupation, timeResults })
        });
        
        if (response.ok) {
            console.log("Data submitted successfully");
        } else {
            console.error("Error submitting data");
        }
    });
});
