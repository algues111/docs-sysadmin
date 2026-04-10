==========
Microsoft
==========

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

   .icon-ad { background: rgba(0, 120, 212, 0.1); color: #0078d4; }
   .icon-exchange { background: rgba(0, 120, 212, 0.1); color: #0078d4; }
   .icon-azure { background: rgba(0, 120, 212, 0.1); color: #0078d4; }
   .icon-powershell { background: rgba(1, 36, 86, 0.1); color: #012456; }
   .icon-server { background: rgba(0, 120, 212, 0.1); color: #0078d4; }
   .icon-intune { background: rgba(0, 120, 212, 0.1); color: #0078d4; }

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
           <div class="ms-stat-number">6</div>
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

       <!-- Active Directory -->
       <a href="Microsoft/ad.html" class="ms-card">
           <div class="ms-card-icon icon-ad">🛡️</div>
           <div class="ms-card-title">Active Directory</div>
           <div class="ms-card-desc">
               Gestion des domaines, GPO, DNS intégré, réplication,
               et administration des utilisateurs et groupes.
           </div>
           <div class="ms-card-tags">
               <span class="ms-tag">GPO</span>
               <span class="ms-tag">DNS</span>
               <span class="ms-tag">LDAP</span>
               <span class="ms-tag">Kerberos</span>
           </div>
           <div class="ms-card-arrow">Explorer →</div>
       </a>

       <!-- Exchange -->
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

       <!-- Azure -->
       <a href="Microsoft/azure.html" class="ms-card">
           <div class="ms-card-icon icon-azure">☁️</div>
           <div class="ms-card-title">Microsoft Azure</div>
           <div class="ms-card-desc">
               Services cloud, Azure AD, machines virtuelles,
               réseau virtuel et gestion des ressources.
           </div>
           <div class="ms-card-tags">
               <span class="ms-tag">Azure AD</span>
               <span class="ms-tag">VNet</span>
               <span class="ms-tag">ARM</span>
           </div>
           <div class="ms-card-arrow">Explorer →</div>
       </a>

       <!-- PowerShell -->
       <a href="Microsoft/powershell.html" class="ms-card">
           <div class="ms-card-icon icon-powershell">⚡</div>
           <div class="ms-card-title">PowerShell</div>
           <div class="ms-card-desc">
               Scripting, automatisation, modules,
               gestion à distance et administration système.
           </div>
           <div class="ms-card-tags">
               <span class="ms-tag">Scripts</span>
               <span class="ms-tag">Modules</span>
               <span class="ms-tag">DSC</span>
           </div>
           <div class="ms-card-arrow">Explorer →</div>
       </a>

       <!-- Windows Server -->
       <a href="Microsoft/windows-server.html" class="ms-card">
           <div class="ms-card-icon icon-server">🖥️</div>
           <div class="ms-card-title">Windows Server</div>
           <div class="ms-card-desc">
               Installation, rôles serveur, Hyper-V,
               clustering et gestion des certificats.
           </div>
           <div class="ms-card-tags">
               <span class="ms-tag">Hyper-V</span>
               <span class="ms-tag">IIS</span>
               <span class="ms-tag">WSUS</span>
           </div>
           <div class="ms-card-arrow">Explorer →</div>
       </a>

       <!-- Intune -->
       <a href="Microsoft/intune.html" class="ms-card">
           <div class="ms-card-icon icon-intune">📱</div>
           <div class="ms-card-title">Microsoft Intune</div>
           <div class="ms-card-desc">
               Gestion des appareils mobiles (MDM), déploiement
               d'applications et politiques de conformité.
           </div>
           <div class="ms-card-tags">
               <span class="ms-tag">MDM</span>
               <span class="ms-tag">MAM</span>
               <span class="ms-tag">Autopilot</span>
           </div>
           <div class="ms-card-arrow">Explorer →</div>
       </a>

   </div>

.. toctree::
   :maxdepth: 2
   :hidden:

   Microsoft/ad
   Microsoft/exchange
   Microsoft/azure
   Microsoft/powershell
   Microsoft/windows-server
   Microsoft/intune
