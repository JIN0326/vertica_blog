---
layout: default
title: Search
permalink: /search/
---

<div class="page-hero">
  <span class="chip">Search</span>
  <h1>Search</h1>
  <p>블로그의 전체 글을 검색할 수 있습니다.</p>
</div>

<div class="content-section content-section-centered" style="max-width: 960px;">
  <div class="search-container">
    <input type="text" id="search-input" placeholder="검색어를 입력하세요..." autofocus>
    <div id="search-results"></div>
  </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/fuse.js/dist/fuse.min.js"></script>
<script>
  (function() {
    const searchInput = document.getElementById('search-input');
    const resultsContainer = document.getElementById('search-results');
    let fuse;
    let searchData;
    let isFetching = false;
    let isInitialized = false;

    function initFuse(data) {
      searchData = data;
      const options = {
        includeScore: true,
        shouldSort: true,
        threshold: 0.4,
        minMatchCharLength: 2,
        keys: [
          { name: 'title', weight: 0.7 },
          { name: 'tags', weight: 0.5 },
          { name: 'category', weight: 0.4 },
          { name: 'content', weight: 0.2 }
        ]
      };
      fuse = new Fuse(searchData, options);
      isInitialized = true;
      if (searchInput.value) {
        performSearch();
      }
    }

    function fetchData() {
      if (isFetching || isInitialized) return;
      isFetching = true;
      fetch('{{ "/search.json" | relative_url }}')
        .then(res => res.json())
        .then(data => {
          initFuse(data);
          isFetching = false;
        })
        .catch(err => {
          console.error("Error fetching search data:", err);
          isFetching = false;
          resultsContainer.innerHTML = '<p class="no-results">검색 데이터를 불러오는 데 실패했습니다.</p>';
        });
    }

    function performSearch() {
      if (!isInitialized) return;
      const query = searchInput.value.trim();

      if (query.length < 2) {
        resultsContainer.innerHTML = '';
        return;
      }

      const results = fuse.search(query);
      displayResults(results, query);
    }

    function displayResults(results, query) {
      if (!results.length) {
        resultsContainer.innerHTML = '<div class="no-results">검색 결과가 없습니다.</div>';
        return;
      }

      let html = '<ul id="search-results-list">';
      results.slice(0, 20).forEach(result => {
        const item = result.item;
        const content = item.content || '';
        let snippet = content.substring(0, 150) + (content.length > 150 ? '...' : '');
        const highlightRegex = new RegExp('(' + query.split(' ').join('|') + ')', 'gi');
        const highlightedSnippet = snippet.replace(highlightRegex, '<mark>$1</mark>');

        html += `<li><a href="{{ site.baseurl }}${item.url}"><h3 class="result-title">${item.title}</h3><p class="result-snippet">${highlightedSnippet}</p></a></li>`;
      });
      html += '</ul>';
      resultsContainer.innerHTML = html;
    }

    let debounceTimer;
    searchInput.addEventListener('input', () => {
      if (!isInitialized) fetchData();
      clearTimeout(debounceTimer);
      debounceTimer = setTimeout(performSearch, 250);
    });

    searchInput.addEventListener('focus', fetchData);
  })();
</script>