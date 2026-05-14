---
title: Vertica 제품 수명주기 및 OS 호환성 - Vertica Blog
layout: default
description: "Vertica 제품 버전별 릴리즈 주기, 기술 지원 종료(EOS) 날짜, 그리고 지원되는 운영체제(OS) 호환성 정보를 안내합니다."
---

<div class="page-hero">
  <span class="chip">Lifecycle</span>
  <h1>Vertica Lifecycle</h1>
  <p>Vertica 제품의 릴리즈 주기, 지원 정책, 그리고 OS 호환성 정보를 안내합니다.</p>
</div>

<div class="content-section content-section-centered lifecycle-page-wrapper" style="max-width: 1200px; margin: 0 auto;">
  
  <div class="content-header-actions">
    <button class="tab-btn active" onclick="switchTab('eos', this)">EOS Info</button>
    <button class="tab-btn" onclick="switchTab('os', this)">Supported OS</button>
  </div>

  <div id="tab-eos" class="tab-content active table-responsive">
    <p class="reference-link">
      <a href="https://www.microfocus.com/productlifecycle/?term=Vertica" target="_blank" rel="noopener">참고 링크: Micro Focus Product Lifecycle</a>
    </p>
    <table class="lifecycle-table">
      <thead>
        <tr>
          <th>Version</th>
          <th>Release Date</th>
          <th>EOS Date</th>
        </tr>
      </thead>
      <tbody>
        <tr><td>Vertica 26.2</td><td>28 Apr 2026</td><td>30 Apr 2029</td></tr>
        <tr><td>Vertica 26.1</td><td>29 Jan 2026</td><td>28 Feb 2029</td></tr>
        <tr><td>Vertica 25.4</td><td>17 Oct 2025</td><td>31 Oct 2028</td></tr>
        <tr><td>Vertica 25.3</td><td>10 Jul 2025</td><td>31 Jul 2028</td></tr>
        <tr><td>Vertica 25.2</td><td>01 Apr 2025</td><td>30 Apr 2028</td></tr>
        <tr><td>Vertica 25.1</td><td>23 Dec 2024</td><td>31 Jan 2028</td></tr>
        <tr><td>Vertica 24.4</td><td>30 Sep 2024</td><td>31 Oct 2027</td></tr>
        <tr><td>Vertica 24.3</td><td>12 Jul 2024</td><td>31 Jul 2027</td></tr>
        <tr><td>Vertica 24.2</td><td>26 Apr 2024</td><td>30 Apr 2027</td></tr>
        <tr><td>Vertica 24.1</td><td>09 Jan 2024</td><td>31 Jan 2027</td></tr>
        <tr><td>Vertica 23.4</td><td>04 Oct 2023</td><td>31 Oct 2026</td></tr>
        <tr><td>Vertica 23.3</td><td>07 Jul 2023</td><td>31 Jul 2026</td></tr>
        <tr><td>Vertica 12.0.4</td><td>05 Apr 2023</td><td>31 Jul 2025</td></tr>
        <tr><td>Vertica 12.0.3</td><td>27 Jan 2023</td><td>31 Jul 2025</td></tr>
        <tr><td>Vertica 12.0.2</td><td>09 Dec 2022</td><td>31 Jul 2025</td></tr>
        <tr><td>Vertica 12.0.1</td><td>01 Sep 2022</td><td>31 Jul 2025</td></tr>
        <tr><td>Vertica 12.0</td><td>01 Sep 2022</td><td>31 Jul 2025</td></tr>
        <tr><td>Vertica 11.1</td><td>24 Feb 2022</td><td>31 Mar 2024</td></tr>
        <tr><td>Vertica 11.0</td><td>11 Aug 2021</td><td>31 Aug 2023</td></tr>
        <tr><td>Vertica 10.1</td><td>03 Feb 2021</td><td>28 Feb 2023</td></tr>
        <tr><td>Vertica 10.0</td><td>11 May 2020</td><td>31 May 2022</td></tr>
        <tr><td>Vertica 9.3</td><td>14 Oct 2019</td><td>31 Oct 2021</td></tr>
        <tr><td>Vertica 9.2</td><td>30 Nov 2018</td><td>31 Dec 2020</td></tr>
        <tr><td>Vertica 9.1</td><td>01 May 2018</td><td>30 Apr 2020</td></tr>
        <tr><td>Vertica 9.0</td><td>13 Oct 2017</td><td>31 Oct 2019</td></tr>
        <tr><td>Vertica 8.1</td><td>N/A</td><td>N/A</td></tr>
        <tr><td>Vertica 7.2</td><td>N/A</td><td>N/A</td></tr>
      </tbody>
    </table>
  </div>

  <div id="tab-os" class="tab-content table-responsive">
    <p class="reference-link">
      <a href="https://docs.vertica.com/26.1.x/en/supported-platforms/server-and-mc/" target="_blank" rel="noopener">참고 링크: Vertica Supported Platforms</a>
    </p>
    <table class="lifecycle-table">
      <thead>
        <tr>
          <th rowspan="2" style="vertical-align: middle;">Vertica_Version</th>
          <th colspan="3">RedHat</th>
          <th colspan="2">Rocky</th>
          <th colspan="3">Ubuntu</th>
          <th colspan="3">Centos</th>
          <th colspan="4">SUSE</th>
          <th colspan="2">openSUSE</th>
          <th colspan="3">Debian</th>
          <th colspan="2">AmazonLinux</th>
        </tr>
        <tr>
          <th>9.x</th><th>8.x</th><th>7.x</th>
          <th>9.x</th><th>8.x</th>
          <th>20.04 & higher</th><th>18.04 & higher</th><th>14.04/16.04/18.04</th>
          <th>9.x</th><th>8.x</th><th>7.x</th>
          <th>15 SP4 & higher</th><th>15.x</th><th>12 SP2 & higher</th><th>12.x</th>
          <th>15.4 & higher</th><th>42.3</th>
          <th>12.x</th><th>10.x</th><th>8.5/8.9</th>
          <th>2023</th><th>2.0</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><strong>26.2</strong></td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td>
        </tr>
        <tr>
          <td><strong>26.1</strong></td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td> <td class="support-o">O</td><td class="support-o">O</td> <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td> <td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td> <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td> <td class="support-o">O</td><td class="support-x">X</td> <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td> <td class="support-o">O</td><td class="support-x">X</td> </tr>
        <tr>
          <td><strong>25.4</strong></td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td>
        </tr>
        <tr>
          <td><strong>25.3</strong></td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td>
        </tr>
        <tr>
          <td><strong>25.2</strong></td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td>
        </tr>
        <tr>
          <td><strong>25.1</strong></td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td>
        </tr>
        <tr>
          <td><strong>24.4</strong></td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-o">O</td><td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-x">X</td>
        </tr>
        <tr>
          <td><strong>24.3</strong></td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-o">O</td><td class="support-x">X</td>
        </tr>
        <tr>
          <td><strong>24.2</strong></td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-o">O</td><td class="support-x">X</td>
        </tr>
        <tr>
          <td><strong>24.1</strong></td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td>
        </tr>
        <tr>
          <td><strong>23.4</strong></td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td>
        </tr>
        <tr>
          <td><strong>23.3</strong></td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td>
        </tr>
        <tr>
          <td><strong>12.0</strong></td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td>
        </tr>
        <tr>
          <td><strong>11.1</strong></td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td>
        </tr>
        <tr>
          <td><strong>11.0</strong></td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td>
        </tr>
        <tr>
          <td><strong>10.1</strong></td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td>
        </tr>
        <tr>
          <td><strong>10.0</strong></td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td>
        </tr>
        <tr>
          <td><strong>9.3</strong></td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-o">O</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td>
        </tr>
        <tr>
          <td><strong>9.2</strong></td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-x">X</td><td class="support-o">O</td>
          <td class="support-x">X</td><td class="support-o">O</td>
        </tr>
      </tbody>
    </table>
  </div>

</div>

<script>
  function switchTab(tabId, btnElement) {
    // 1. 모든 탭 콘텐츠 숨기기
    document.querySelectorAll('.tab-content').forEach(function(content) {
      content.classList.remove('active');
    });
    
    // 2. 모든 버튼에서 active 클래스 제거
    document.querySelectorAll('.content-header-actions .tab-btn').forEach(function(btn) {
      btn.classList.remove('active');
    });
    
    // 3. 선택된 탭과 버튼 활성화
    document.getElementById('tab-' + tabId).classList.add('active');
    btnElement.classList.add('active');
  }
</script>