<script>
    let sqlInput = '';
    let sqliteOutput = '';

    async function convertSqlToSqlite() {
        const formData = new FormData();
        formData.append('inputFiles[]', new Blob([sqlInput], { type: 'text/plain' }), 'input.sql');
        formData.append('outputFormat', 'sqlite');
        try {
            const response = await fetch('https://www.rebasedata.com/api/v1/convert', {
                method: 'POST',
                body: formData
            });
            if (!response.ok) {
                throw new Error(`Error: ${response.statusText}`);
            }
            const blob = await response.blob();
            const url = window.URL.createObjectURL(blob);
            sqliteOutput = url;
        } catch (error) {
            console.error('Error converting SQL to SQLite:', error);
            sqliteOutput = `Error: ${error.message}`;
        }
    }
</script>

<style>
    .container {
        max-width: 600px;
        margin: 0 auto;
        padding: 2rem;
        background-color: #f9f9f9;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        font-family: Arial, sans-serif;
    }
    h1 {
        text-align: center;
        color: #333;
    }
    .input-area, .output-area {
        margin-bottom: 1.5rem;
    }
    label {
        display: block;
        margin-bottom: 0.5rem;
        font-weight: bold;
        color: #555;
    }
    textarea {
        width: 100%;
        padding: 0.5rem;
        border: 1px solid #ddd;
        border-radius: 4px;
        font-family: monospace;
    }
    button {
        display: block;
        width: 100%;
        padding: 0.75rem;
        background-color: #007BFF;
        color: white;
        border: none;
        border-radius: 4px;
        font-size: 1rem;
        cursor: pointer;
        transition: background-color 0.3s;
    }
    button:hover {
        background-color: #0056b3;
    }
    .output-link {
        display: block;
        margin-top: 1rem;
        color: #007BFF;
        text-decoration: none;
        word-wrap: break-word;
    }
    .output-link:hover {
        text-decoration: underline;
    }
    p.error {
        color: red;
        font-weight: bold;
    }
</style>

<div class="container">
    <h1>SQL to SQLite Converter</h1>
    <div class="input-area">
        <label for="sql-input">SQL Input:</label>
        <textarea id="sql-input" bind:value={sqlInput} rows="10" cols="50"></textarea>
    </div>
    <button on:click={convertSqlToSqlite}>Convert to SQLite</button>
    {#if sqliteOutput}
        <div class="output-area">
            <label>SQLite Output:</label>
            {#if sqliteOutput.startsWith('Error:')}
                <p class="error">{sqliteOutput}</p>
            {:else}
                <a class="output-link" href={sqliteOutput} download="output.sqlite">Download SQLite file</a>
            {/if}
        </div>
    {/if}
</div>
