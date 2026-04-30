# Search Results

<div id="search-results-container" class="mrgn-tp-lg" markdown="1">
  <p>Loading results...</p>
</div>

<script>
document.addEventListener("DOMContentLoaded", function() {
    const urlParams = new URLSearchParams(window.location.search);
    const query = urlParams.get('q');
    const container = document.getElementById('search-results-container');

    if (!query) {
        container.innerHTML = '<div class="alert alert-info"><p>Please enter a keyword in the search bar above.</p></div>';
        return;
    }

    container.innerHTML = '<h2>Results for: <strong>' + query + '</strong></h2><ul class="list-unstyled mrgn-tp-md" id="results-list"></ul>';
    const resultsList = document.getElementById('results-list');

    // Fetch the locally compiled MkDocs search index
    fetch('./search_index.json')
        .then(response => response.json())
        .then(data => {
            let matchCount = 0;
            
            // Loop through all documents in the site
            data.docs.forEach(doc => {
                // Case-insensitive check on title and content
                if ((doc.title && doc.title.toLowerCase().includes(query.toLowerCase())) || 
                    (doc.text && doc.text.toLowerCase().includes(query.toLowerCase()))) {
                    
                    matchCount++;
                    const li = document.createElement('li');
                    li.className = "mrgn-bttm-lg";
                    
                    // Clean up the text preview (strip markdown hashes)
                    const preview = doc.text.substring(0, 200).replace(/#/g, '') + '...';
                    
                    // Link back to the root level
                    const link = '../' + doc.location; 
                    
                    li.innerHTML = `
                        <h3 class="h4 mrgn-bttm-sm"><a href="${link}">${doc.title}</a></h3>
                        <p>${preview}</p>
                    `;
                    resultsList.appendChild(li);
                }
            });

            if (matchCount === 0) {
                container.innerHTML += '<div class="alert alert-warning"><p>No matching documents found.</p></div>';
            }
        })
        .catch(err => {
            container.innerHTML = '<div class="alert alert-danger"><p>Error loading the search index.</p></div>';
            console.error(err);
        });
});
</script>
