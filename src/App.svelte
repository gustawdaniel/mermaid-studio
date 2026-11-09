<script lang="ts">
	import { onMount } from 'svelte';
	import mermaid from 'mermaid';
	import panzoom, { type PanZoom } from 'panzoom';

	type Lang = 'en' | 'pl';

	interface SavedDiagram {
		id: string;
		title: string;
		code: string;
		updatedAt: number;
		starred?: boolean;
	}

	interface TemplateDef {
		label: string;
		icon: string;
		code: string;
	}

	const I18N = {
		en: {
			title: 'Mermaid Studio',
			subtitle: 'Interactive Live Diagram Editor',
			history: 'History',
			editor: 'Editor',
			newDiagram: 'New Diagram',
			newDiagramTooltip: 'Create new diagram (saves current)',
			chooseTemplate: 'Choose Starter Template:',
			themeLight: 'Light',
			themeDark: 'Dark',
			themeToggleTitle: 'Toggle Theme (Dark / Light)',
			langToggleTitle: 'Switch Language (EN / PL)',
			zoomIn: 'Zoom In',
			zoomOut: 'Zoom Out',
			zoomReset: 'Reset View (1:1)',
			save: 'Save',
			saveTooltip: 'Save to history (Ctrl+S)',
			copyMd: 'Copy MD',
			copyMdTooltip: 'Copy as Markdown fenced block',
			svg: 'SVG',
			svgTooltip: 'Copy SVG code',
			png: 'PNG 📷',
			pngTooltip: 'Download PNG image',
			editorTitle: 'MERMAID CODE EDITOR',
			tabIndent: 'Tab: indent',
			ctrlSave: 'Ctrl+S: save',
			ctrlEnter: 'Ctrl+Enter: render',
			placeholder: 'Type or paste Mermaid diagram code here...',
			savedDiagrams: 'Saved Diagrams',
			quickNew: '+ Create New Diagram',
			starTooltip: 'Add to favorites',
			deleteTooltip: 'Delete diagram',
			confirmDelete: 'Are you sure you want to delete this diagram?',
			toastSaved: 'Diagram saved to history! 💾',
			toastDeleted: 'Diagram deleted',
			toastCopiedMd: 'Markdown block copied to clipboard! 📋',
			toastCopiedSvg: 'SVG code copied to clipboard! 🖼️',
			toastCopiedPng: 'PNG image downloaded! 📷',
			toastNewCreated: 'New diagram ready for editing! 🚀',
			urlLoaded: 'Diagram loaded from URL parameter',
			hideEditor: 'Hide editor panel',
			showEditor: 'Show editor panel',
			dragResize: 'Drag to resize editor & preview',
			moreTemplates: 'Browse More Templates (mermaideditor.io)',
			templatesLibrary: 'Templates',
			defaultTitle: 'Cloud Architecture & Microservices'
		},
		pl: {
			title: 'Mermaid Studio',
			subtitle: 'Interaktywny edytor diagramów live',
			history: 'Historia',
			editor: 'Edytor',
			newDiagram: 'Nowy diagram',
			newDiagramTooltip: 'Stwórz nowy diagram (zapisze bieżący)',
			chooseTemplate: 'Wybierz Szablon Startowy:',
			themeLight: 'Jasny',
			themeDark: 'Ciemny',
			themeToggleTitle: 'Przełącz motyw (Ciemny / Jasny)',
			langToggleTitle: 'Zmień język (PL / EN)',
			zoomIn: 'Przybliż',
			zoomOut: 'Oddal',
			zoomReset: 'Reset widoku (1:1)',
			save: 'Zapisz',
			saveTooltip: 'Zapisz do historii (Ctrl+S)',
			copyMd: 'Kopiuj MD',
			copyMdTooltip: 'Kopiuj gotowy blok markdown',
			svg: 'SVG',
			svgTooltip: 'Kopiuj kod SVG',
			png: 'PNG 📷',
			pngTooltip: 'Pobierz obraz PNG',
			editorTitle: 'EDYCJA KODU MERMAID',
			tabIndent: 'Tab: wcięcie',
			ctrlSave: 'Ctrl+S: zapisz',
			ctrlEnter: 'Ctrl+Enter: render',
			placeholder: 'Wklej lub napisz diagram Mermaid...',
			savedDiagrams: 'Twoje Diagramy',
			quickNew: '+ Utwórz Nowy Diagram',
			starTooltip: 'Dodaj do ulubionych',
			deleteTooltip: 'Usuń diagram',
			confirmDelete: 'Czy na pewno chcesz usunąć ten diagram?',
			toastSaved: 'Diagram zapisany w historii! 💾',
			toastDeleted: 'Diagram usunięty',
			toastCopiedMd: 'Skopiowano blok Markdown do schowka! 📋',
			toastCopiedSvg: 'Skopiowano kod SVG do schowka! 🖼️',
			toastCopiedPng: 'Pobrano obraz PNG! 📷',
			toastNewCreated: 'Nowy diagram gotowy do edycji! 🚀',
			urlLoaded: 'Załadowano diagram z parametru URL',
			hideEditor: 'Schowaj edytor',
			showEditor: 'Pokaż edytor',
			dragResize: 'Przeciągnij, aby zmienić rozmiar',
			moreTemplates: 'Więcej szablonów (mermaideditor.io)',
			templatesLibrary: 'Szablony',
			defaultTitle: 'Architektura Chmurowa & Mikrousługi'
		}
	};

	const TEMPLATES: Record<Lang, Record<string, TemplateDef>> = {
		en: {
			architecture: {
				label: 'Cloud Architecture (Microservices)',
				icon: '🏛️',
				code: `graph TD
    subgraph Clients["Clients & Endpoints"]
        Web["Single Page App<br/>(TypeScript / Svelte / React)"]
        Mobile["Mobile Client<br/>(iOS & Android Apps)"]
        CLI["Developer CLI<br/>(Automated Agent / Script)"]
    end

    subgraph Edge["Edge & Security Gateway"]
        CDN["Global Edge CDN<br/>(DDoS Protection & SSL Offload)"]
        Gateway["API Gateway<br/>(Auth Verification, Rate Limiting)"]
        
        Web --> CDN
        Mobile --> CDN
        CLI --> Gateway
        CDN --> Gateway
    end

    subgraph Core["Microservices Core"]
        AuthSvc["Identity & Auth<br/>(OAuth2 / OIDC / JWT)"]
        ApiSvc["Main Business API<br/>(REST & GraphQL Endpoints)"]
        WorkerSvc["Asynchronous Worker<br/>(Background Processing)"]
        
        Gateway --> AuthSvc
        Gateway --> ApiSvc
    end

    subgraph Messaging["Messaging & Event Bus"]
        Cache[("Redis Cache<br/>(In-Memory Store)")]
        EventBus[("Event Stream<br/>(Kafka / NATS Broker)")]
        
        ApiSvc <--> Cache
        ApiSvc -->|"Publish events"| EventBus
        EventBus -->|"Consume events"| WorkerSvc
    end

    subgraph Storage["Persistence & Telemetry"]
        DB[("PostgreSQL Cluster<br/>(Primary Relational Data)")]
        ObjStore[("Object Storage<br/>(S3 / Blob Storage)")]
        Metrics[("Metrics & Tracing<br/>(OpenTelemetry / Prometheus)")]
        
        ApiSvc --> DB
        WorkerSvc --> DB
        WorkerSvc --> ObjStore
        ApiSvc --> Metrics
    end`
			},
			flowchart: {
				label: 'Flowchart (Decisions & Steps)',
				icon: '⚡',
				code: `flowchart TD
    Start(["Start Project"]) --> Step1["Step 1: Define Requirements & Architecture"]
    Step1 --> Review{"Design Approved?"}
    Review -->|No| Rework["Revise Specifications"]
    Rework --> Step1
    Review -->|Yes| Dev["Step 2: Implement Code & Tests"]
    Dev --> CI{"CI & Automated Checks Pass?"}
    CI -->|Failed| Fix["Fix Bugs & Lint Issues"]
    Fix --> Dev
    CI -->|Passed| Deploy(["Deploy to Production"])`
			},
			sequence: {
				label: 'Sequence Diagram (OAuth2 Auth)',
				icon: '🔄',
				code: `sequenceDiagram
    autonumber
    actor User as End User
    participant App as Web Application
    participant Gateway as API Gateway
    participant Auth as Auth Service
    participant API as Core Business API
    participant DB as Database

    User->>App: Click "Sign In"
    App->>Auth: Initiate Login (OAuth2 Flow)
    Auth-->>App: Return Authorization Code
    App->>Gateway: POST /auth/token (Exchange Code)
    Gateway->>Auth: Verify Code & Credentials
    Auth-->>Gateway: Identity Verified
    Gateway-->>App: Issue Session Token (JWT)

    User->>App: Request Profile Data
    App->>Gateway: GET /api/v1/profile (Bearer JWT)
    Gateway->>Gateway: Verify Token Signature
    Gateway->>API: Forward Request with Claims
    API->>DB: Fetch User Record
    DB-->>API: Return User Entity
    API-->>Gateway: HTTP 200 OK (JSON Payload)
    Gateway-->>App: Render Profile View`
			},
			git: {
				label: 'Git Graph (Gitflow & Releases)',
				icon: '🌿',
				code: `gitGraph
    commit id: "Initial commit (v1.0.0)"
    branch develop
    checkout develop
    commit id: "Setup project structure"
    branch feature/user-auth
    checkout feature/user-auth
    commit id: "Implement JWT handler"
    commit id: "Add auth tests"
    checkout develop
    merge feature/user-auth id: "Merge feature/user-auth"
    branch release/v1.1.0
    checkout release/v1.1.0
    commit id: "Bump version 1.1.0"
    checkout main
    merge release/v1.1.0 id: "Release v1.1.0" tag: "v1.1.0"
    checkout develop
    merge release/v1.1.0 id: "Sync release to develop"`
			},
			class: {
				label: 'Class Diagram (Domain Model)',
				icon: '📦',
				code: `classDiagram
    class User {
        +UUID id
        +String email
        +String fullName
        +Boolean isActive
        +register()
        +authenticate()
    }
    class Order {
        +UUID orderId
        +DateTime createdAt
        +OrderStatus status
        +Decimal totalAmount
        +calculateTotal()
        +cancel()
    }
    class OrderItem {
        +UUID productId
        +Integer quantity
        +Decimal unitPrice
    }
    class Product {
        +UUID id
        +String sku
        +String title
        +Decimal price
        +Integer stockLevel
    }
    User "1" *-- "*" Order : places
    Order "1" *-- "*" OrderItem : contains
    Product "1" -- "*" OrderItem : references`
			},
			mindmap: {
				label: 'Mindmap (System Architecture)',
				icon: '🧠',
				code: `mindmap
  root((System Design))
    Frontend
      SvelteKit / React
      Tailwind CSS / Vanilla
      WebSockets & SSE
      PWA & Offline Cache
    Backend
      FastAPI / Go / Node
      REST & GraphQL
      gRPC Microservices
    Data Layer
      PostgreSQL
      Redis Caching
      Vector Search pgvector
    DevOps & Cloud
      Docker & Containers
      CI/CD GitHub Actions
      Caddy & Cloudflare
      Prometheus & OpenTelemetry`
			},
			blank: {
				label: 'Blank Canvas',
				icon: '📄',
				code: `flowchart TD\n    A["New Node"] --> B["Next Step"]`
			}
		},
		pl: {
			architecture: {
				label: 'Architektura Chmurowa (Mikrousługi)',
				icon: '🏛️',
				code: `graph TD
    subgraph Klienci["Klienci & Punkty Końcowe"]
        Web["Aplikacja Webowa<br/>(TypeScript / Svelte / React)"]
        Mobile["Aplikacja Mobilna<br/>(iOS & Android)"]
        CLI["Narzędzie CLI<br/>(Automatyczny Agent / Skrypt)"]
    end

    subgraph Brzeg["Brama Brzegowa & Bezpieczeństwo"]
        CDN["Globalne Edge CDN<br/>(Ochrona DDoS & Caching SSL)"]
        Gateway["Brama API Gateway<br/>(Weryfikacja Auth, Rate Limiting)"]
        
        Web --> CDN
        Mobile --> CDN
        CLI --> Gateway
        CDN --> Gateway
    end

    subgraph Rdzen["Rdzeń Mikrousług"]
        AuthSvc["Identyfikacja & Auth<br/>(OAuth2 / OIDC / JWT)"]
        ApiSvc["Główny Serwis API<br/>(Punkty REST & GraphQL)"]
        WorkerSvc["Zadania w Tle<br/>(Przetwarzanie Asynchroniczne)"]
        
        Gateway --> AuthSvc
        Gateway --> ApiSvc
    end

    subgraph Kolejki["Komunikacja & Pamięć Podręczna"]
        Cache[("Pamięć Redis Cache<br/>(Magazyn w Pamięci)")]
        EventBus[("Szyna Zdarzeń<br/>(Broker Kafka / NATS)")]
        
        ApiSvc <--> Cache
        ApiSvc -->|"Publikacja zdarzeń"| EventBus
        EventBus -->|"Odbiór zadań"| WorkerSvc
    end

    subgraph Dane["Trwałość Danych & Telemetria"]
        DB[("Klaster PostgreSQL<br/>(Główna Relacyjna Baza)")]
        ObjStore[("Magazyn Obiektowy<br/>(Blob / Bucket S3)")]
        Metrics[("Metryki & Tracing<br/>(OpenTelemetry / Prometheus)")]
        
        ApiSvc --> DB
        WorkerSvc --> DB
        WorkerSvc --> ObjStore
        ApiSvc --> Metrics
    end`
			},
			flowchart: {
				label: 'Flowchart (Decyzje & Kroki)',
				icon: '⚡',
				code: `flowchart TD
    Start(["Start Projektu"]) --> Krok1["Krok 1: Wymagania i Architektura"]
    Krok1 --> Decyzja{"Czy projekt zatwierdzony?"}
    Decyzja -->|Nie| Poprawka["Poprawa specyfikacji"]
    Poprawka --> Krok1
    Decyzja -->|Tak| Kodowanie["Krok 2: Implementacja i testy"]
    Kodowanie --> Testy{"Czy testy automatyczne przechodzą?"}
    Testy -->|Błąd| Fix["Naprawa błędów i linting"]
    Fix --> Kodowanie
    Testy -->|Sukces| Wdrozenie(["Wdrożenie na produkcję"])`
			},
			sequence: {
				label: 'Diagram Sekwencji (Autoryzacja OAuth2)',
				icon: '🔄',
				code: `sequenceDiagram
    autonumber
    actor User as Użytkownik
    participant App as Aplikacja Webowa
    participant Gateway as API Gateway
    participant Auth as Serwis Auth
    participant API as Główny Serwis API
    participant DB as Baza Danych

    User->>App: Kliknięcie "Zaloguj"
    App->>Auth: Inicjalizacja logowania (OAuth2)
    Auth-->>App: Zwrot kodu autoryzacyjnego
    App->>Gateway: POST /auth/token (Wymiana kodu)
    Gateway->>Auth: Weryfikacja tożsamości
    Auth-->>Gateway: Potwierdzenie tożsamości
    Gateway-->>App: Wydanie tokenu sesji (Signed JWT)

    User->>App: Żądanie danych profilu
    App->>Gateway: GET /api/v1/profile (Bearer JWT)
    Gateway->>Gateway: Weryfikacja podpisu tokenu
    Gateway->>API: Przekazanie zapytania z danymi użytkownika
    API->>DB: Pobranie rekordu z bazy
    DB-->>API: Zwrócenie danych
    API-->>Gateway: HTTP 200 OK (JSON)
    Gateway-->>App: Renderowanie profilu w interfejsie`
			},
			git: {
				label: 'Git Graph (Gitflow & Wydania)',
				icon: '🌿',
				code: `gitGraph
    commit id: "Initial commit (v1.0.0)"
    branch develop
    checkout develop
    commit id: "Inicjalizacja struktury projektu"
    branch feature/user-auth
    checkout feature/user-auth
    commit id: "Implementacja modułu JWT"
    commit id: "Dodanie testów jednostkowych"
    checkout develop
    merge feature/user-auth id: "Scalenie feature/user-auth"
    branch release/v1.1.0
    checkout release/v1.1.0
    commit id: "Aktualizacja wersji 1.1.0"
    checkout main
    merge release/v1.1.0 id: "Wydanie v1.1.0" tag: "v1.1.0"
    checkout develop
    merge release/v1.1.0 id: "Synchronizacja do develop"`
			},
			class: {
				label: 'Diagram Klas (Model Domenowy)',
				icon: '📦',
				code: `classDiagram
    class User {
        +UUID id
        +String email
        +String fullName
        +Boolean isActive
        +register()
        +authenticate()
    }
    class Order {
        +UUID orderId
        +DateTime createdAt
        +OrderStatus status
        +Decimal totalAmount
        +calculateTotal()
        +cancel()
    }
    class OrderItem {
        +UUID productId
        +Integer quantity
        +Decimal unitPrice
    }
    class Product {
        +UUID id
        +String sku
        +String title
        +Decimal price
        +Integer stockLevel
    }
    User "1" *-- "*" Order : składa
    Order "1" *-- "*" OrderItem : zawiera
    Product "1" -- "*" OrderItem : dotyczy`
			},
			mindmap: {
				label: 'Mindmap (Stos Technologiczny)',
				icon: '🧠',
				code: `mindmap
  root((Architektura Systemu))
    Frontend
      SvelteKit / React
      Tailwind CSS / Vanilla
      WebSockets & SSE
      PWA & Offline Cache
    Backend
      FastAPI / Go / Node
      REST & GraphQL
      Mikrousługi gRPC
    Warstwa Danych
      PostgreSQL
      Redis Caching
      Vector Search pgvector
    DevOps & Chmura
      Docker & Kontenery
      CI/CD GitHub Actions
      Caddy & Cloudflare
      Prometheus & OpenTelemetry`
			},
			blank: {
				label: 'Czysty Arkusz',
				icon: '📄',
				code: `flowchart TD\n    A["Nowy węzeł"] --> B["Następny krok"]`
			}
		}
	};

	// Language state (default: English)
	let currentLang = $state<Lang>('en');
	let t = $derived(I18N[currentLang]);
	let currentTemplates = $derived(TEMPLATES[currentLang]);

	// Workspace panel layout state
	let editorOpen = $state(true);
	let editorWidth = $state(45); // percentage (15% - 85%)
	let isDragging = $state(false);
	let workspaceBody: HTMLDivElement | null = null;

	// Diagram code and elements
	let code = $state('');
	let renderContainer: HTMLDivElement | null = null;
	let canvasElement: HTMLDivElement | null = null;
	let editorTextarea: HTMLTextAreaElement | null = null;
	let pzInstance: PanZoom | null = null;

	let themeMode = $state<'dark' | 'light'>('dark');
	let errorMessage = $state<string | null>(null);
	let isRendering = $state(false);
	let sidebarOpen = $state(false);
	let templateMenuOpen = $state(false);
	let savedDiagrams = $state<SavedDiagram[]>([]);
	let activeId = $state<string>('current');
	let toastMessage = $state<string | null>(null);

	function showToast(msg: string) {
		toastMessage = msg;
		setTimeout(() => {
			if (toastMessage === msg) toastMessage = null;
		}, 2500);
	}

	function initMermaid(isLight: boolean) {
		mermaid.initialize({
			startOnLoad: false,
			theme: isLight ? 'default' : 'dark',
			securityLevel: 'loose',
			fontFamily: 'Outfit, -apple-system, sans-serif',
			themeVariables: isLight
				? {
						darkMode: false,
						background: '#ffffff',
						primaryColor: '#f1f5f9',
						primaryTextColor: '#0f172a',
						primaryBorderColor: '#0284c7',
						lineColor: '#64748b',
						secondaryColor: '#e0f2fe',
						tertiaryColor: '#f8fafc',
						edgeLabelBackground: '#ffffff'
				  }
				: {
						darkMode: true,
						background: '#0d121d',
						primaryColor: '#1e293b',
						primaryTextColor: '#f1f5f9',
						primaryBorderColor: '#38bdf8',
						lineColor: '#94a3b8',
						secondaryColor: '#1e1b4b',
						tertiaryColor: '#0f172a',
						edgeLabelBackground: '#0d121d'
				  }
		});
	}

	function toggleTheme() {
		themeMode = themeMode === 'dark' ? 'light' : 'dark';
		try {
			localStorage.setItem('mermaid_studio_theme', themeMode);
		} catch {}
		document.documentElement.setAttribute('data-theme', themeMode);
		initMermaid(themeMode === 'light');
		triggerRender();
		showToast(themeMode === 'light' ? 'Light mode active ☀️' : 'Dark mode active 🌙');
	}

	function toggleLanguage() {
		currentLang = currentLang === 'en' ? 'pl' : 'en';
		try {
			localStorage.setItem('mermaid_studio_lang', currentLang);
		} catch {}
		showToast(currentLang === 'en' ? 'Language switched to English 🇬🇧' : 'Zmieniono język na polski 🇵🇱');
	}

	function toggleEditorPanel() {
		editorOpen = !editorOpen;
		try {
			localStorage.setItem('mermaid_studio_editor_open', String(editorOpen));
		} catch {}
		setTimeout(() => {
			if (pzInstance) resetZoom();
		}, 50);
	}

	// Resizer dragging logic
	function startDragging(e: MouseEvent) {
		e.preventDefault();
		isDragging = true;
		window.addEventListener('mousemove', handleMouseMove);
		window.addEventListener('mouseup', stopDragging);
	}

	function handleMouseMove(e: MouseEvent) {
		if (!isDragging || !workspaceBody) return;
		const rect = workspaceBody.getBoundingClientRect();
		const newWidthPct = ((e.clientX - rect.left) / rect.width) * 100;
		if (newWidthPct >= 15 && newWidthPct <= 85) {
			editorWidth = Math.round(newWidthPct * 10) / 10;
		}
	}

	function stopDragging() {
		if (isDragging) {
			isDragging = false;
			window.removeEventListener('mousemove', handleMouseMove);
			window.removeEventListener('mouseup', stopDragging);
			try {
				localStorage.setItem('mermaid_studio_editor_width', String(editorWidth));
			} catch {}
			if (pzInstance) {
				setTimeout(() => resetZoom(), 30);
			}
		}
	}

	function extractTitle(text: string): string {
		const titleMatch = text.match(/(?:title|accTitle):\s*(.+)/i);
		if (titleMatch) return titleMatch[1].trim();
		const lines = text.split('\n').map((l) => l.trim()).filter((l) => l && !l.startsWith('%%'));
		for (const line of lines) {
			const nodeMatch = line.match(/\["([^"]+)"\]|\[([^\]]+)\]|\("([^"]+)"\)|\(([^)]+)\)/);
			if (nodeMatch) return nodeMatch[1] || nodeMatch[2] || nodeMatch[3] || nodeMatch[4];
			if (line.startsWith('subgraph')) {
				const sg = line.replace('subgraph', '').trim();
				return sg.replace(/\["([^"]+)"\]|\[([^\]]+)\]/, '$1$2').trim();
			}
		}
		return (currentLang === 'pl' ? 'Diagram ' : 'Diagram ') + new Date().toLocaleTimeString(currentLang === 'pl' ? 'pl-PL' : 'en-US', { hour: '2-digit', minute: '2-digit' });
	}

	function loadHistoryFromStorage() {
		try {
			if (typeof window !== 'undefined') {
				let passedCode: string | null = null;
				if (window.location.hash && window.location.hash.includes('code=')) {
					try {
						passedCode = decodeURIComponent(window.location.hash.split('code=')[1]);
					} catch (e) {
						passedCode = window.location.hash.split('code=')[1];
					}
				} else if (window.location.search && window.location.search.includes('code=')) {
					const params = new URLSearchParams(window.location.search);
					passedCode = params.get('code');
				}

				if (passedCode && passedCode.trim()) {
					code = passedCode.trim();
					activeId = 'url_' + Date.now();
					const raw = localStorage.getItem('mermaid_studio_history');
					if (raw) savedDiagrams = JSON.parse(raw);
					return;
				}
			}

			const defaultInitialCode = TEMPLATES[currentLang]['architecture'].code;

			const raw = localStorage.getItem('mermaid_studio_history');
			if (raw) {
				savedDiagrams = JSON.parse(raw);
			} else {
				savedDiagrams = [
					{
						id: 'default-1',
						title: t.defaultTitle,
						code: defaultInitialCode,
						updatedAt: Date.now(),
						starred: true
					}
				];
				saveHistoryToStorage();
			}

			const currentCached = localStorage.getItem('mermaid_studio_current');
			if (currentCached && currentCached.trim()) {
				code = currentCached;
			} else {
				code = defaultInitialCode;
			}
		} catch (e) {
			console.warn('Failed to load history from localStorage', e);
		}
	}

	function saveHistoryToStorage() {
		try {
			localStorage.setItem('mermaid_studio_history', JSON.stringify(savedDiagrams));
		} catch (e) {
			console.warn('Failed to save history to localStorage', e);
		}
	}

	function saveCurrentDiagram(silent = false) {
		const title = extractTitle(code);
		const existingIndex = savedDiagrams.findIndex((d) => d.id === activeId);
		if (existingIndex >= 0 && !activeId.startsWith('current')) {
			savedDiagrams[existingIndex].code = code;
			savedDiagrams[existingIndex].title = title;
			savedDiagrams[existingIndex].updatedAt = Date.now();
		} else {
			const newId = 'diag_' + Date.now();
			activeId = newId;
			savedDiagrams = [
				{
					id: newId,
					title,
					code,
					updatedAt: Date.now()
				},
				...savedDiagrams
			];
		}
		saveHistoryToStorage();
		if (!silent) {
			showToast(t.toastSaved);
		}
	}

	function createNewDiagram(templateKey = 'flowchart') {
		if (code.trim().length > 30) {
			saveCurrentDiagram(true);
		}

		const chosen = currentTemplates[templateKey] || currentTemplates['flowchart'];
		code = chosen.code;
		activeId = 'current_' + Date.now();
		templateMenuOpen = false;
		if (window.innerWidth < 768) sidebarOpen = false;

		triggerRender();
		resetZoom();

		setTimeout(() => {
			editorTextarea?.focus();
		}, 50);

		showToast(t.toastNewCreated);
	}

	function selectSavedDiagram(d: SavedDiagram) {
		activeId = d.id;
		code = d.code;
		triggerRender();
		resetZoom();
		if (window.innerWidth < 768) sidebarOpen = false;
	}

	function deleteSavedDiagram(id: string, e: MouseEvent) {
		e.stopPropagation();
		savedDiagrams = savedDiagrams.filter((d) => d.id !== id);
		saveHistoryToStorage();
		if (activeId === id) activeId = 'current';
		showToast(t.toastDeleted);
	}

	function toggleStar(id: string, e: MouseEvent) {
		e.stopPropagation();
		savedDiagrams = savedDiagrams.map((d) => (d.id === id ? { ...d, starred: !d.starred } : d));
		saveHistoryToStorage();
	}

	let debounceTimer: any = null;
	function handleCodeInput() {
		try {
			localStorage.setItem('mermaid_studio_current', code);
		} catch {}
		clearTimeout(debounceTimer);
		debounceTimer = setTimeout(() => {
			triggerRender();
		}, 300);
	}

	async function triggerRender() {
		if (!canvasElement) return;
		isRendering = true;
		try {
			const id = 'render_' + Math.random().toString(36).substring(2, 9);
			const { svg } = await mermaid.render(id, code);
			errorMessage = null;
			if (canvasElement) {
				canvasElement.innerHTML = svg;
				setupPanzoom();
			}
		} catch (err: any) {
			console.warn('Mermaid render error:', err);
			errorMessage = err.message || String(err);
		} finally {
			isRendering = false;
		}
	}

	function setupPanzoom() {
		if (!canvasElement) return;
		if (pzInstance) {
			pzInstance.dispose();
			pzInstance = null;
		}
		const svgEl = canvasElement.querySelector('svg');
		if (svgEl) {
			svgEl.style.width = '100%';
			svgEl.style.height = '100%';
			pzInstance = panzoom(svgEl, {
				maxZoom: 8,
				minZoom: 0.15,
				smoothScroll: true,
				zoomDoubleClickSpeed: 1
			});
		}
	}

	function zoomIn() {
		if (pzInstance && canvasElement) {
			const rect = canvasElement.getBoundingClientRect();
			pzInstance.smoothZoom(rect.width / 2, rect.height / 2, 1.3);
		}
	}

	function zoomOut() {
		if (pzInstance && canvasElement) {
			const rect = canvasElement.getBoundingClientRect();
			pzInstance.smoothZoom(rect.width / 2, rect.height / 2, 0.7);
		}
	}

	function resetZoom() {
		if (pzInstance) {
			pzInstance.moveTo(0, 0);
			pzInstance.zoomAbs(0, 0, 1);
		}
	}

	function copyMarkdown() {
		const md = `\`\`\`mermaid\n${code}\n\`\`\``;
		navigator.clipboard.writeText(md);
		showToast(t.toastCopiedMd);
	}

	function copySvg() {
		if (!canvasElement) return;
		const svgEl = canvasElement.querySelector('svg');
		if (!svgEl) return;
		navigator.clipboard.writeText(svgEl.outerHTML);
		showToast(t.toastCopiedSvg);
	}

	function downloadPng() {
		if (!canvasElement) return;
		const svgEl = canvasElement.querySelector('svg');
		if (!svgEl) return;

		const svgString = new XMLSerializer().serializeToString(svgEl);
		const svgBlob = new Blob([svgString], { type: 'image/svg+xml;charset=utf-8' });
		const URL = window.URL || window.webkitURL || window;
		const blobURL = URL.createObjectURL(svgBlob);
		const image = new Image();

		image.onload = () => {
			const canvas = document.createElement('canvas');
			const scale = 2;
			canvas.width = (svgEl.clientWidth || 1000) * scale;
			canvas.height = (svgEl.clientHeight || 700) * scale;
			const context = canvas.getContext('2d');
			if (!context) return;
			
			context.fillStyle = themeMode === 'light' ? '#ffffff' : '#080b11';
			context.fillRect(0, 0, canvas.width, canvas.height);
			context.drawImage(image, 0, 0, canvas.width, canvas.height);

			const a = document.createElement('a');
			a.download = `mermaid_${Date.now()}.png`;
			a.href = canvas.toDataURL('image/png');
			document.body.appendChild(a);
			a.click();
			document.body.removeChild(a);
			showToast(t.toastCopiedPng);
		};
		image.src = blobURL;
	}

	function handleEditorKeydown(e: KeyboardEvent) {
		if (e.key === 'Tab') {
			e.preventDefault();
			const textarea = e.target as HTMLTextAreaElement;
			const start = textarea.selectionStart;
			const end = textarea.selectionEnd;
			code = code.substring(0, start) + '  ' + code.substring(end);
			setTimeout(() => {
				textarea.selectionStart = textarea.selectionEnd = start + 2;
			}, 0);
		} else if ((e.ctrlKey || e.metaKey) && e.key === 's') {
			e.preventDefault();
			saveCurrentDiagram();
		} else if ((e.ctrlKey || e.metaKey) && e.key === 'Enter') {
			e.preventDefault();
			triggerRender();
		}
	}

	onMount(() => {
		// Load language
		const savedLang = localStorage.getItem('mermaid_studio_lang') as Lang | null;
		if (savedLang === 'en' || savedLang === 'pl') {
			currentLang = savedLang;
		}

		// Load theme
		const savedTheme = localStorage.getItem('mermaid_studio_theme') as 'dark' | 'light' | null;
		if (savedTheme === 'light' || savedTheme === 'dark') {
			themeMode = savedTheme;
		}
		document.documentElement.setAttribute('data-theme', themeMode);
		initMermaid(themeMode === 'light');

		// Load editor layout
		const savedWidth = localStorage.getItem('mermaid_studio_editor_width');
		if (savedWidth) {
			const w = parseFloat(savedWidth);
			if (w >= 15 && w <= 85) editorWidth = w;
		}
		const savedEditorOpen = localStorage.getItem('mermaid_studio_editor_open');
		if (savedEditorOpen !== null) {
			editorOpen = savedEditorOpen === 'true';
		}

		loadHistoryFromStorage();
		triggerRender();

		const handleClickOutside = (e: MouseEvent) => {
			const target = e.target as HTMLElement;
			if (!target.closest('.new-menu-container')) {
				templateMenuOpen = false;
			}
		};
		window.addEventListener('click', handleClickOutside);

		return () => {
			window.removeEventListener('click', handleClickOutside);
			window.removeEventListener('mousemove', handleMouseMove);
			window.removeEventListener('mouseup', stopDragging);
			if (pzInstance) pzInstance.dispose();
		};
	});
</script>

<svelte:head>
	<title>{t.title} • {t.subtitle}</title>
</svelte:head>

<div class="studio-root">
	<!-- Top Navigation Bar -->
	<header class="studio-header">
		<div class="brand">
			<!-- History Toggle Button -->
			<button
				class="panel-toggle-btn {sidebarOpen ? 'active' : ''}"
				onclick={() => (sidebarOpen = !sidebarOpen)}
				title={t.savedDiagrams}
			>
				<svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
					<line x1="3" y1="12" x2="21" y2="12"></line>
					<line x1="3" y1="6" x2="21" y2="6"></line>
					<line x1="3" y1="18" x2="21" y2="18"></line>
				</svg>
				<span>{t.history} ({savedDiagrams.length})</span>
			</button>

			<!-- Editor Toggle Button (Collapsible) -->
			<button
				class="panel-toggle-btn {editorOpen ? 'active' : ''}"
				onclick={toggleEditorPanel}
				title={editorOpen ? t.hideEditor : t.showEditor}
			>
				<svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
					<rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect>
					<line x1="9" y1="3" x2="9" y2="21"></line>
				</svg>
				<span>{t.editor}</span>
			</button>

			<!-- Prominent New Diagram Button Group -->
			<div class="new-menu-container">
				<button 
					class="new-diagram-btn"
					onclick={() => createNewDiagram('flowchart')}
					title={t.newDiagramTooltip}
				>
					<span class="plus-icon">+</span>
					<span>{t.newDiagram}</span>
				</button>
				<button 
					class="new-dropdown-trigger {templateMenuOpen ? 'open' : ''}"
					onclick={(e) => {
						e.stopPropagation();
						templateMenuOpen = !templateMenuOpen;
					}}
					title={t.chooseTemplate}
				>
					▾
				</button>

				{#if templateMenuOpen}
					<div class="template-dropdown">
						<div class="dropdown-header">{t.chooseTemplate}</div>
						{#each Object.entries(currentTemplates) as [key, tmpl]}
							<button 
								class="dropdown-item"
								onclick={() => createNewDiagram(key)}
							>
								<span class="item-icon">{tmpl.icon}</span>
								<span class="item-label">{tmpl.label}</span>
							</button>
						{/each}
						<div class="dropdown-divider"></div>
						<a
							href="https://www.mermaideditor.io/templates"
							target="_blank"
							rel="noopener noreferrer"
							class="dropdown-item external-link"
							title={t.moreTemplates}
							onclick={() => (templateMenuOpen = false)}
						>
							<span class="item-icon">🌐</span>
							<span class="item-label">{t.moreTemplates} <span class="ext-arrow">↗</span></span>
						</a>
					</div>
				{/if}
			</div>

			<div class="title-wrap">
				<h1>📊 {t.title}</h1>
				<span class="sub">{t.subtitle}</span>
			</div>
		</div>

		<div class="header-tools">
			<!-- External Templates Link -->
			<a
				href="https://www.mermaideditor.io/templates"
				target="_blank"
				rel="noopener noreferrer"
				class="templates-link-btn"
				title={t.moreTemplates}
			>
				<span class="btn-icon">📚</span>
				<span class="btn-text">{t.templatesLibrary}</span>
				<span class="ext-arrow">↗</span>
			</a>

			<!-- Language Toggle (EN / PL) -->
			<button
				class="lang-toggle-btn"
				onclick={toggleLanguage}
				title={t.langToggleTitle}
			>
				<span class="lang-flag">{currentLang === 'en' ? '🇬🇧' : '🇵🇱'}</span>
				<span class="lang-code">{currentLang.toUpperCase()}</span>
			</button>

			<!-- Theme Toggle (Dark / Light) -->
			<button
				class="theme-toggle-btn"
				onclick={toggleTheme}
				title={t.themeToggleTitle}
			>
				{#if themeMode === 'dark'}
					<span class="theme-icon">☀️</span>
					<span class="theme-text">{t.themeLight}</span>
				{:else}
					<span class="theme-icon">🌙</span>
					<span class="theme-text">{t.themeDark}</span>
				{/if}
			</button>

			<!-- Zoom Controls -->
			<div class="zoom-controls">
				<button class="tool-btn" onclick={zoomIn} title={t.zoomIn}>+</button>
				<button class="tool-btn" onclick={zoomOut} title={t.zoomOut}>-</button>
				<button class="tool-btn" onclick={resetZoom} title={t.zoomReset}>1:1</button>
			</div>

			<!-- Actions -->
			<div class="action-buttons">
				<button class="action-btn save-btn" onclick={() => saveCurrentDiagram(false)} title={t.saveTooltip}>
					<svg width="15" height="15" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
						<path d="M19 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11l5 5v11a2 2 0 0 1-2 2z"></path>
						<polyline points="17 21 17 13 7 13 7 21"></polyline>
						<polyline points="7 3 7 8 15 8"></polyline>
					</svg>
					<span>{t.save}</span>
				</button>
				<button class="action-btn" onclick={copyMarkdown} title={t.copyMdTooltip}>
					<span>{t.copyMd}</span>
				</button>
				<button class="action-btn" onclick={copySvg} title={t.svgTooltip}>
					<span>{t.svg}</span>
				</button>
				<button class="action-btn download-btn" onclick={downloadPng} title={t.pngTooltip}>
					<span>{t.png}</span>
				</button>
			</div>
		</div>
	</header>

	<!-- Main Workspace -->
	<div 
		class="workspace-body" 
		bind:this={workspaceBody} 
		class:is-resizing={isDragging}
	>
		<!-- Sidebar History Panel -->
		{#if sidebarOpen}
			<aside class="sidebar-history">
				<div class="sidebar-header">
					<h3>{t.savedDiagrams}</h3>
					<button class="close-sidebar-btn" onclick={() => (sidebarOpen = false)}>✕</button>
				</div>

				<div class="sidebar-quick-new">
					<button class="sidebar-new-btn" onclick={() => createNewDiagram('flowchart')}>
						<span>{t.quickNew}</span>
					</button>
				</div>

				<div class="history-list">
					{#each savedDiagrams as d (d.id)}
						<div
							class="history-item {activeId === d.id ? 'active' : ''}"
							onclick={() => selectSavedDiagram(d)}
							role="button"
							tabindex="0"
						>
							<div class="history-item-top">
								<span class="item-title">{d.title}</span>
								<button
									class="star-btn {d.starred ? 'starred' : ''}"
									onclick={(e) => toggleStar(d.id, e)}
									title={t.starTooltip}
								>
									{d.starred ? '★' : '☆'}
								</button>
							</div>
							<div class="history-item-bottom">
								<span class="item-date">{new Date(d.updatedAt).toLocaleDateString(currentLang === 'pl' ? 'pl-PL' : 'en-US', { day: 'numeric', month: 'short' })}</span>
								<button
									class="delete-btn"
									onclick={(e) => deleteSavedDiagram(d.id, e)}
									title={t.deleteTooltip}
								>
									🗑️
								</button>
							</div>
						</div>
					{/each}
				</div>
			</aside>
		{/if}

		<!-- Editor Left Panel (Collapsible & Resizable) -->
		{#if editorOpen}
			<section class="editor-pane" style="width: {editorWidth}%;">
				<div class="editor-header">
					<span class="editor-title">{t.editorTitle}</span>
					<div class="editor-hints">
						<span class="hint">{t.tabIndent}</span>
						<span class="hint">{t.ctrlSave}</span>
						<span class="hint">{t.ctrlEnter}</span>
					</div>
				</div>
				<textarea
					class="code-textarea"
					bind:this={editorTextarea}
					bind:value={code}
					oninput={handleCodeInput}
					onkeydown={handleEditorKeydown}
					spellcheck="false"
					placeholder={t.placeholder}
				></textarea>
				{#if errorMessage}
					<div class="error-banner">
						<span class="err-icon">⚠️</span>
						<pre class="err-text">{errorMessage}</pre>
					</div>
				{/if}
			</section>

			<!-- Resizable Splitter Bar -->
			<div 
				class="resizer-bar"
				onmousedown={startDragging}
				role="separator"
				aria-orientation="vertical"
				tabindex="0"
				title={t.dragResize}
			>
				<div class="resizer-handle"></div>
			</div>
		{/if}

		<!-- Canvas Preview Panel (Flex 1 - Expands to 100% when editor is hidden) -->
		<section class="canvas-pane" bind:this={renderContainer}>
			<div class="canvas-overlay-grid"></div>
			<div class="canvas-viewport" bind:this={canvasElement}></div>
		</section>
	</div>

	<!-- Toast Popup -->
	{#if toastMessage}
		<div class="toast-popup">
			<span>{toastMessage}</span>
		</div>
	{/if}
</div>

<style>
	.studio-root {
		display: flex;
		flex-direction: column;
		height: 100vh;
		width: 100vw;
		background: var(--bg-base);
		color: var(--text-primary);
	}

	.studio-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 0.55rem 1.25rem;
		background: var(--bg-subtle);
		border-bottom: 1px solid var(--border-card);
		gap: 1rem;
		flex-wrap: wrap;
		z-index: 20;
	}

	.brand {
		display: flex;
		align-items: center;
		gap: 0.75rem;
	}

	/* Panel Toggle Buttons (History & Editor) */
	.panel-toggle-btn {
		display: inline-flex;
		align-items: center;
		gap: 0.4rem;
		background: var(--bg-card);
		border: 1px solid var(--border-card);
		color: var(--text-secondary);
		padding: 0.45rem 0.8rem;
		border-radius: var(--radius-sm);
		font-size: 0.82rem;
		font-weight: 500;
		cursor: pointer;
		transition: all 0.2s;
	}
	.panel-toggle-btn:hover {
		background: var(--bg-card-hover);
		color: var(--text-primary);
		border-color: var(--border-hover);
	}
	.panel-toggle-btn.active {
		background: rgba(6, 182, 212, 0.15);
		border-color: var(--accent-cyan);
		color: var(--accent-cyan);
	}

	/* Prominent New Diagram Button Group */
	.new-menu-container {
		display: inline-flex;
		position: relative;
		border-radius: var(--radius-sm);
		box-shadow: 0 2px 10px rgba(6, 182, 212, 0.2);
	}

	.new-diagram-btn {
		display: inline-flex;
		align-items: center;
		gap: 0.4rem;
		background: linear-gradient(135deg, #0284c7 0%, #06b6d4 100%);
		border: 1px solid rgba(255, 255, 255, 0.2);
		border-right: 1px solid rgba(255, 255, 255, 0.25);
		color: #ffffff;
		padding: 0.45rem 0.85rem;
		border-top-left-radius: var(--radius-sm);
		border-bottom-left-radius: var(--radius-sm);
		font-size: 0.84rem;
		font-weight: 600;
		cursor: pointer;
		transition: all 0.2s;
	}
	.new-diagram-btn:hover {
		filter: brightness(1.1);
	}
	.plus-icon {
		font-size: 1.1rem;
		line-height: 1;
		font-weight: 700;
	}

	.new-dropdown-trigger {
		background: #0284c7;
		border: 1px solid rgba(255, 255, 255, 0.2);
		border-left: none;
		color: #ffffff;
		padding: 0.45rem 0.6rem;
		border-top-right-radius: var(--radius-sm);
		border-bottom-right-radius: var(--radius-sm);
		cursor: pointer;
		font-size: 0.8rem;
		transition: all 0.2s;
	}
	.new-dropdown-trigger:hover,
	.new-dropdown-trigger.open {
		background: #0369a1;
	}

	.template-dropdown {
		position: absolute;
		top: calc(100% + 6px);
		left: 0;
		min-width: 260px;
		background: var(--bg-card);
		border: 1px solid var(--border-card);
		border-radius: var(--radius-md);
		box-shadow: 0 10px 30px rgba(0, 0, 0, 0.4);
		padding: 0.4rem;
		display: flex;
		flex-direction: column;
		gap: 0.2rem;
		z-index: 50;
		animation: dropFade 0.15s ease-out;
	}

	@keyframes dropFade {
		from { opacity: 0; transform: translateY(-4px); }
		to { opacity: 1; transform: translateY(0); }
	}

	.dropdown-header {
		font-size: 0.7rem;
		text-transform: uppercase;
		letter-spacing: 0.05em;
		color: var(--text-muted);
		padding: 0.4rem 0.6rem 0.2rem;
		font-weight: 700;
	}

	.dropdown-item {
		display: flex;
		align-items: center;
		gap: 0.6rem;
		width: 100%;
		padding: 0.5rem 0.75rem;
		background: transparent;
		border: none;
		color: var(--text-primary);
		border-radius: var(--radius-sm);
		font-size: 0.82rem;
		text-align: left;
		cursor: pointer;
		transition: background 0.15s;
	}
	.dropdown-item:hover {
		background: var(--bg-card-hover);
		color: var(--accent-cyan);
	}
	.dropdown-divider {
		height: 1px;
		background: var(--border-card);
		margin: 0.35rem 0.2rem;
	}
	.dropdown-item.external-link {
		text-decoration: none;
		color: var(--accent-cyan);
		font-weight: 500;
	}
	.dropdown-item.external-link:hover {
		background: rgba(56, 189, 248, 0.12);
	}
	.item-icon {
		font-size: 1rem;
	}
	.ext-arrow {
		font-size: 0.75rem;
		opacity: 0.8;
		margin-left: auto;
	}

	.templates-link-btn {
		display: inline-flex;
		align-items: center;
		gap: 0.35rem;
		padding: 0.35rem 0.65rem;
		background: var(--bg-card);
		border: 1px solid var(--border-card);
		border-radius: var(--radius-sm);
		color: var(--text-secondary);
		text-decoration: none;
		font-size: 0.8rem;
		font-weight: 600;
		transition: all 0.15s ease;
	}
	.templates-link-btn:hover {
		background: var(--bg-card-hover);
		border-color: var(--accent-cyan);
		color: var(--accent-cyan);
		transform: translateY(-1px);
	}
	.templates-link-btn .btn-icon {
		font-size: 0.9rem;
	}
	.templates-link-btn .ext-arrow {
		font-size: 0.75rem;
		opacity: 0.7;
		margin-left: 0.1rem;
	}

	.title-wrap {
		display: flex;
		flex-direction: column;
		margin-left: 0.25rem;
	}
	.title-wrap h1 {
		font-size: 0.95rem;
		font-weight: 700;
		margin: 0;
		line-height: 1.2;
		color: var(--text-primary);
	}
	.title-wrap .sub {
		font-size: 0.7rem;
		color: var(--text-muted);
	}

	.header-tools {
		display: flex;
		align-items: center;
		gap: 0.75rem;
	}

	/* Language Switcher */
	.lang-toggle-btn {
		display: inline-flex;
		align-items: center;
		gap: 0.35rem;
		background: var(--bg-input);
		border: 1px solid var(--border-card);
		color: var(--text-primary);
		padding: 0.35rem 0.65rem;
		border-radius: var(--radius-sm);
		font-size: 0.78rem;
		font-weight: 600;
		cursor: pointer;
		transition: all 0.2s;
	}
	.lang-toggle-btn:hover {
		background: var(--bg-card-hover);
		border-color: var(--accent-cyan);
		color: var(--accent-cyan);
	}
	.lang-flag {
		font-size: 0.9rem;
		line-height: 1;
	}

	/* Theme Toggle */
	.theme-toggle-btn {
		display: inline-flex;
		align-items: center;
		gap: 0.4rem;
		background: var(--bg-input);
		border: 1px solid var(--border-card);
		color: var(--text-secondary);
		padding: 0.35rem 0.7rem;
		border-radius: var(--radius-sm);
		font-size: 0.8rem;
		font-weight: 500;
		cursor: pointer;
		transition: all 0.2s;
	}
	.theme-toggle-btn:hover {
		background: var(--bg-card-hover);
		color: var(--text-primary);
	}
	.theme-icon {
		font-size: 0.95rem;
	}

	.zoom-controls {
		display: flex;
		background: var(--bg-input);
		border: 1px solid var(--border-card);
		border-radius: var(--radius-sm);
		overflow: hidden;
	}

	.tool-btn {
		background: none;
		border: none;
		color: var(--text-secondary);
		padding: 0.35rem 0.65rem;
		cursor: pointer;
		font-size: 0.85rem;
		border-right: 1px solid var(--border-subtle);
	}
	.tool-btn:last-child {
		border-right: none;
	}
	.tool-btn:hover {
		background: var(--bg-card-hover);
		color: var(--text-primary);
	}

	.action-buttons {
		display: flex;
		gap: 0.5rem;
	}

	.action-btn {
		background: var(--bg-card);
		border: 1px solid var(--border-card);
		color: var(--text-secondary);
		padding: 0.35rem 0.75rem;
		border-radius: var(--radius-sm);
		font-size: 0.8rem;
		font-weight: 500;
		cursor: pointer;
		transition: all 0.2s;
		display: inline-flex;
		align-items: center;
		gap: 0.35rem;
	}

	.action-btn:hover {
		background: var(--bg-card-hover);
		color: var(--text-primary);
		border-color: var(--border-hover);
	}

	.save-btn {
		background: rgba(6, 182, 212, 0.12);
		border-color: rgba(6, 182, 212, 0.4);
		color: var(--accent-cyan);
	}
	.save-btn:hover {
		background: rgba(6, 182, 212, 0.22);
		border-color: var(--accent-cyan);
	}

	.download-btn:hover {
		border-color: var(--accent-emerald);
		color: var(--accent-emerald);
	}

	/* Workspace layout */
	.workspace-body {
		display: flex;
		flex: 1;
		position: relative;
		overflow: hidden;
	}

	.workspace-body.is-resizing {
		cursor: col-resize;
		user-select: none;
	}
	.workspace-body.is-resizing .canvas-viewport {
		pointer-events: none;
	}

	/* Sidebar History */
	.sidebar-history {
		width: 290px;
		background: var(--bg-subtle);
		border-right: 1px solid var(--border-card);
		display: flex;
		flex-direction: column;
		z-index: 10;
		box-shadow: 4px 0 16px rgba(0, 0, 0, 0.1);
	}

	.sidebar-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 0.75rem 1rem;
		border-bottom: 1px solid var(--border-subtle);
	}
	.sidebar-header h3 {
		font-size: 0.82rem;
		font-weight: 700;
		color: var(--text-secondary);
		text-transform: uppercase;
		letter-spacing: 0.05em;
		margin: 0;
	}

	.close-sidebar-btn {
		background: none;
		border: none;
		color: var(--text-muted);
		cursor: pointer;
		font-size: 0.95rem;
		padding: 0.2rem 0.4rem;
	}
	.close-sidebar-btn:hover {
		color: var(--text-primary);
	}

	.sidebar-quick-new {
		padding: 0.6rem 0.8rem;
		border-bottom: 1px solid var(--border-subtle);
	}

	.sidebar-new-btn {
		width: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
		background: var(--bg-card);
		border: 1px dashed var(--accent-cyan);
		color: var(--accent-cyan);
		padding: 0.5rem;
		border-radius: var(--radius-sm);
		font-size: 0.82rem;
		font-weight: 600;
		cursor: pointer;
		transition: all 0.2s;
	}
	.sidebar-new-btn:hover {
		background: rgba(6, 182, 212, 0.1);
	}

	.history-list {
		flex: 1;
		overflow-y: auto;
		padding: 0.6rem;
		display: flex;
		flex-direction: column;
		gap: 0.4rem;
	}

	.history-item {
		padding: 0.65rem 0.75rem;
		background: var(--bg-card);
		border: 1px solid var(--border-card);
		border-radius: var(--radius-sm);
		cursor: pointer;
		transition: all 0.15s;
	}
	.history-item:hover {
		background: var(--bg-card-hover);
		border-color: var(--border-hover);
	}

	.history-item.active {
		background: rgba(6, 182, 212, 0.12);
		border-color: var(--accent-cyan);
	}

	.history-item-top {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 0.25rem;
	}

	.item-title {
		font-size: 0.85rem;
		font-weight: 500;
		color: var(--text-primary);
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}

	.star-btn {
		background: none;
		border: none;
		color: var(--text-muted);
		font-size: 0.9rem;
		cursor: pointer;
	}
	.star-btn.starred {
		color: var(--accent-amber);
	}

	.history-item-bottom {
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.item-date {
		font-size: 0.72rem;
		color: var(--text-muted);
	}

	.delete-btn {
		background: none;
		border: none;
		font-size: 0.75rem;
		cursor: pointer;
		opacity: 0.5;
	}
	.delete-btn:hover {
		opacity: 1;
	}

	/* Editor Pane */
	.editor-pane {
		min-width: 240px;
		display: flex;
		flex-direction: column;
		background: var(--bg-subtle);
		position: relative;
		overflow: hidden;
	}

	.editor-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 0.5rem 1rem;
		border-bottom: 1px solid var(--border-subtle);
		background: var(--bg-card);
		flex-shrink: 0;
	}

	.editor-title {
		font-size: 0.72rem;
		font-family: var(--font-mono);
		font-weight: 600;
		letter-spacing: 0.08em;
		color: var(--accent-cyan);
	}

	.editor-hints {
		display: flex;
		gap: 0.6rem;
	}

	.hint {
		font-size: 0.68rem;
		color: var(--text-muted);
		font-family: var(--font-mono);
	}

	.code-textarea {
		flex: 1;
		width: 100%;
		background: transparent;
		color: var(--text-primary);
		font-family: var(--font-mono);
		font-size: 0.85rem;
		line-height: 1.6;
		padding: 1rem;
		border: none;
		resize: none;
		outline: none;
		white-space: pre;
	}

	.error-banner {
		background: rgba(244, 63, 94, 0.15);
		border-top: 1px solid var(--accent-rose);
		padding: 0.6rem 1rem;
		display: flex;
		align-items: flex-start;
		gap: 0.5rem;
		flex-shrink: 0;
	}

	.err-text {
		font-family: var(--font-mono);
		font-size: 0.72rem;
		color: var(--accent-rose);
		white-space: pre-wrap;
		max-height: 80px;
		overflow-y: auto;
		margin: 0;
	}

	/* Resizer Splitter Bar */
	.resizer-bar {
		width: 6px;
		background: var(--border-card);
		cursor: col-resize;
		position: relative;
		transition: background 0.15s;
		z-index: 5;
		display: flex;
		align-items: center;
		justify-content: center;
		user-select: none;
		flex-shrink: 0;
	}

	.resizer-bar:hover,
	.workspace-body.is-resizing .resizer-bar {
		background: var(--accent-cyan);
	}

	.resizer-handle {
		width: 2px;
		height: 24px;
		background: var(--text-muted);
		border-radius: 1px;
		pointer-events: none;
	}

	/* Canvas Pane (Expands to 100% when editor is hidden) */
	.canvas-pane {
		flex: 1;
		position: relative;
		overflow: hidden;
		background: var(--bg-base);
		display: flex;
		align-items: center;
		justify-content: center;
		min-width: 0;
	}

	.canvas-overlay-grid {
		position: absolute;
		inset: 0;
		background-image: 
			linear-gradient(to right, var(--grid-line) 1px, transparent 1px),
			linear-gradient(to bottom, var(--grid-line) 1px, transparent 1px);
		background-size: 24px 24px;
		pointer-events: none;
		z-index: 1;
	}

	.canvas-viewport {
		width: 100%;
		height: 100%;
		z-index: 2;
		display: flex;
		align-items: center;
		justify-content: center;
		cursor: grab;
	}

	.canvas-viewport:active {
		cursor: grabbing;
	}

	/* Toast */
	.toast-popup {
		position: fixed;
		bottom: 1.5rem;
		right: 1.5rem;
		background: var(--bg-card);
		border: 1px solid var(--accent-cyan);
		color: var(--text-primary);
		padding: 0.55rem 1rem;
		border-radius: var(--radius-sm);
		font-size: 0.85rem;
		box-shadow: 0 4px 20px rgba(0, 0, 0, 0.25);
		z-index: 100;
	}

	@media (max-width: 768px) {
		.studio-header {
			padding: 0.6rem;
		}
		.title-wrap {
			display: none;
		}
		.workspace-body {
			flex-direction: column;
		}
		.editor-pane {
			width: 100% !important;
			height: 40%;
		}
		.resizer-bar {
			display: none;
		}
		.canvas-pane {
			width: 100%;
			height: 60%;
		}
	}
</style>
