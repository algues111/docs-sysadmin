Microsoft
=========

.. raw:: html

   <style>
   /* ===== CONTAINER PRINCIPAL ===== */
   .ms-hero {
       background: linear-gradient(135deg, #0078d4 0%, #00bcf2 100%);
       border-radius: 16px;
       padding: 40px;
       margin-bottom: 40px;
       color: white;
       text-align: center;
       box-shadow: 0 4px 20px rgba(0, 120, 212, 0.3);
   }

   .ms-hero h2 {
       font-size: 2.2em;
       margin-bottom: 10px;
       color: white !important;
   }

   .ms-hero p {
       font-size: 1.2em;
       opacity: 0.9;
   }

   /* ===== GRILLE DE CARTES ===== */
   .ms-grid {
       display: grid;
       grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
       gap: 24px;
       margin: 30px 0;
   }

   .ms-card {
       background: var(--pst-color-background);
       border: 1px solid var(--pst-color-border);
       border-radius: 12px;
       padding: 28px;
       transition: all 0.3s ease;
       text-decoration: none !important;
       color: inherit !important;
       display: flex;
       flex-direction: column;
       position: relative;
       overflow: hidden;
   }

   .ms-card:hover {
       transform: translateY(-6px);
       box-shadow: 0 12px 30px rgba(0, 0, 0, 0.15);
       border-color: #0078d4;
   }

   .ms-card::before {
       content: '';
       position: absolute;
       top: 0;
       left: 0;
       right: 0;
       height: 4px;
       background: linear-gradient(90deg, #0078d4, #00bcf2);
       opacity: 0;
       transition: opacity 0.3s ease;
   }

   .ms-card:hover::before {
       opacity: 1;
   }

   /* ===== ICÔNES ===== */
   .ms-card-icon {
       width: 56px;
       height: 56px;
       border-radius: 12px;
       display: flex;
       align-items: center;
       justify-content: center;
       font-size: 28px;
       margin-bottom: 16px;
   }

   .icon-server { background: rgba(0, 120, 212, 0.1); color: #0078d4; }
   .icon-exchange { background: rgba(0, 120, 212, 0.1); color: #0078d4; }
   .icon-ms365 { background: rgba(0, 120, 212, 0.1); color: #0078d4; }

   .ms-card-title {
       font-size: 1.3em;
       font-weight: 700;
       margin-bottom: 8px;
       color: var(--pst-color-text-base);
   }

   .ms-card-desc {
       font-size: 0.95em;
       color: var(--pst-color-text-muted);
       line-height: 1.6;
       flex-grow: 1;
   }

   .ms-card-tags {
       display: flex;
       flex-wrap: wrap;
       gap: 6px;
       margin-top: 16px;
   }

   .ms-tag {
       background: rgba(0, 120, 212, 0.08);
       color: #0078d4;
       padding: 4px 10px;
       border-radius: 20px;
       font-size: 0.78em;
       font-weight: 600;
   }

   .ms-card-arrow {
       margin-top: 16px;
       color: #0078d4;
       font-weight: 600;
       font-size: 0.9em;
       opacity: 0;
       transition: opacity 0.3s ease;
   }

   .ms-card:hover .ms-card-arrow {
       opacity: 1;
   }

   /* ===== CARTE WINDOWS SERVER (LARGE) ===== */
   .ms-card-large {
       grid-column: 1 / -1;
       background: var(--pst-color-background);
       border: 1px solid var(--pst-color-border);
       border-radius: 12px;
       padding: 28px;
       position: relative;
       overflow: hidden;
   }

   .ms-card-large::before {
       content: '';
       position: absolute;
       top: 0;
       left: 0;
       right: 0;
       height: 4px;
       background: linear-gradient(90deg, #0078d4, #00bcf2);
   }

   .ms-card-large-header {
       display: flex;
       align-items: center;
       gap: 16px;
       margin-bottom: 24px;
   }

   .ms-card-large-header .ms-card-icon {
       margin-bottom: 0;
   }

   .ms-card-large-title {
       font-size: 1.4em;
       font-weight: 700;
       color: var(--pst-color-text-base);
   }

   .ms-card-large-desc {
       font-size: 0.95em;
       color: var(--pst-color-text-muted);
   }

   /* Sous-grille dans la carte Windows Server */
   .ms-subgrid {
       display: grid;
       grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
       gap: 16px;
   }

   .ms-subcard {
       background: var(--pst-color-surface);
       border: 1px solid var(--pst-color-border);
       border-radius: 10px;
       padding: 20px;
       transition: all 0.3s ease;
       text-decoration: none !important;
       color: inherit !important;
       display: flex;
       flex-direction: column;
   }

   .ms-subcard:hover {
       transform: translateY(-4px);
       box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
       border-color: #0078d4;
   }

   .ms-subcard-icon {
       font-size: 24px;
       margin-bottom: 10px;
   }

   .ms-subcard-title {
       font-size: 1.05em;
       font-weight: 700;
       margin-bottom: 6px;
       color: var(--pst-color-text-base);
   }

   .ms-subcard-desc {
       font-size: 0.85em;
       color: var(--pst-color-text-muted);
       line-height: 1.5;
       flex-grow: 1;
   }

   .ms-subcard-arrow {
       margin-top: 12px;
       color: #0078d4;
       font-weight: 600;
       font-size: 0.85em;
       opacity: 0;
       transition: opacity 0.3s ease;
   }

   .ms-subcard:hover .ms-subcard-arrow {
       opacity: 1;
   }

   /* ===== STATS BAR ===== */
   .ms-stats {
       display: flex;
       justify-content: center;
       gap: 40px;
       margin: -20px 0 30px 0;
       padding: 20px;
       background: var(--pst-color-background);
       border-radius: 12px;
       border: 1px solid var(--pst-color-border);
       box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
   }

   .ms-stat {
       text-align: center;
   }

   .ms-stat-number {
       font-size: 1.8em;
       font-weight: 800;
       color: #0078d4;
   }

   .ms-stat-label {
       font-size: 0.85em;
       color: var(--pst-color-text-muted);
       margin-top: 4px;
   }

   /* ===== RESPONSIVE ===== */
   @media (max-width: 768px) {
       .ms-hero { padding: 24px; }
       .ms-hero h2 { font-size: 1.6em; }
       .ms-grid { grid-template-columns: 1fr; }
       .ms-subgrid { grid-template-columns: 1fr; }
       .ms-stats { flex-direction: column; gap: 16px; }
   }
   </style>

   <!-- ===== HERO BANNER ===== -->
   <div class="ms-hero">
       <h2>🏢 Écosystème Microsoft</h2>
       <p>Documentation complète des services et outils Microsoft pour l'administration système</p>
   </div>

   <!-- ===== STATS BAR ===== -->
   <div class="ms-stats">
       <div class="ms-stat">
           <div class="ms-stat-number">3</div>
           <div class="ms-stat-label">Technologies</div>
       </div>
       <div class="ms-stat">
           <div class="ms-stat-number">25+</div>
           <div class="ms-stat-label">Articles</div>
       </div>
       <div class="ms-stat">
           <div class="ms-stat-number">100+</div>
           <div class="ms-stat-label">Commandes</div>
       </div>
   </div>

   <!-- ===== GRILLE DE CARTES ===== -->
   <div class="ms-grid">

       <!-- ===== WINDOWS SERVER (CARTE LARGE AVEC SOUS-CARTES) ===== -->
       <div class="ms-card-large">
           <div class="ms-card-large-header">
               <div class="ms-card-icon icon-server">🖥️</div>
               <div>
                   <div class="ms-card-large-title">Windows Server</div>
                   <div class="ms-card-large-desc">Installation, rôles serveur, et services d'infrastructure</div>
               </div>
           </div>
           <div class="ms-subgrid">

               <!-- Active Directory -->
               <a href="Microsoft/winserv/ad.html" class="ms-subcard">
                   <div class="ms-subcard-icon">🛡️</div>
                   <div class="ms-subcard-title">Active Directory</div>
                   <div class="ms-subcard-desc">
                       Gestion des domaines, GPO, DNS intégré, réplication et administration des utilisateurs.
                   </div>
                   <div class="ms-subcard-arrow">Explorer →</div>
               </a>

               <!-- DFS -->
               <a href="Microsoft/winserv/dfs.html" class="ms-subcard">
                   <div class="ms-subcard-icon">📂</div>
                   <div class="ms-subcard-title">DFS</div>
                   <div class="ms-subcard-desc">
                       Système de fichiers distribués, espaces de noms et réplication DFS-R.
                   </div>
                   <div class="ms-subcard-arrow">Explorer →</div>
               </a>

               <!-- Partage -->
               <a href="Microsoft/winserv/partage.html" class="ms-subcard">
                   <div class="ms-subcard-icon">🔗</div>
                   <div class="ms-subcard-title">Partage réseau</div>
                   <div class="ms-subcard-desc">
                       Partages SMB, permissions NTFS, quotas et gestion des accès.
                   </div>
                   <div class="ms-subcard-arrow">Explorer →</div>
               </a>

           </div>
       </div>

       <!-- ===== WINDOWS 11 (CARTE LARGE AVEC SOUS-CARTES) ===== -->
       <div class="ms-card-large">
           <div class="ms-card-large-header">
               <div class="ms-card-icon icon-server">💻</div>
               <div>
                   <div class="ms-card-large-title">Windows 11</div>
                   <div class="ms-card-large-desc">Installation, Configuration, Troubleshooting...</div>
               </div>
           </div>
           <div class="ms-subgrid">

               <!-- Active Directory -->
               <a href="Microsoft/install-win11.html" class="ms-subcard">
                   <div class="ms-subcard-icon">🏗️</div>
                   <div class="ms-subcard-title">Installation</div>
                   <div class="ms-subcard-desc">
                       lorem ipsum
                   </div>
                   <div class="ms-subcard-arrow">Explorer →</div>
               </a>

               <!-- DFS -->
               <a href="Microsoft/.html" class="ms-subcard">
                   <div class="ms-subcard-icon">🔧</div>
                   <div class="ms-subcard-title">Configuration</div>
                   <div class="ms-subcard-desc">
                       lorem, ipsum
                   </div>
                   <div class="ms-subcard-arrow">Explorer →</div>
               </a>

               <!-- Partage -->
               <a href="Microsoft/partage.html" class="ms-subcard">
                   <div class="ms-subcard-icon">🔗</div>
                   <div class="ms-subcard-title">Partage réseau</div>
                   <div class="ms-subcard-desc">
                       Partages SMB, permissions NTFS, quotas et gestion des accès.
                   </div>
                   <div class="ms-subcard-arrow">Explorer →</div>
               </a>

           </div>
       </div>

       <!-- ===== EXCHANGE ===== -->
       <a href="Microsoft/exchange.html" class="ms-card">
           <div class="ms-card-icon icon-exchange">📧</div>
           <div class="ms-card-title">Exchange Server</div>
           <div class="ms-card-desc">
               Configuration de la messagerie, gestion des boîtes aux lettres,
               règles de transport et haute disponibilité.
           </div>
           <div class="ms-card-tags">
               <span class="ms-tag">SMTP</span>
               <span class="ms-tag">DAG</span>
               <span class="ms-tag">OWA</span>
           </div>
           <div class="ms-card-arrow">Explorer →</div>
       </a>

       <!-- ===== MICROSOFT 365 ===== -->
       <a href="Microsoft/ms365.html" class="ms-card">
           <div class="ms-card-icon icon-ms365">☁️</div>
           <div class="ms-card-title">Microsoft 365</div>
           <div class="ms-card-desc">
               Administration du tenant M365, gestion des licences,
               Exchange Online, SharePoint et Teams.
           </div>
           <div class="ms-card-tags">
               <span class="ms-tag">Teams</span>
               <span class="ms-tag">SharePoint</span>
               <span class="ms-tag">Exchange Online</span>
           </div>
           <div class="ms-card-arrow">Explorer →</div>
       </a>

   </div>

.. toctree::
   :maxdepth: 2
   :hidden:

   Microsoft/exchange
   Microsoft/ms365
   Microsoft/winserv
   Microsoft/win11
