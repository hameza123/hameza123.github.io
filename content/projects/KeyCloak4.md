+++
date = '2025-10-05T13:30:30+01:00'
draft = true
title = "Keycloak : Part 4"
description = "A practical introduction to Keycloak OAuth 2.0 grant flows with real examples and security analysis"
tags = ["keycloak", "oauth2", "security", "iam", "authentication"]
slug = ""
categories = ["cybersecurity", "tutorial"]
series = []
+++






<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>OAuth 2.0 Authorization Code Grant Flow Demo</title>
    <style>
        :root {
            --primary: #2c3e50;
            --secondary: #3498db;
            --success: #27ae60;
            --warning: #f39c12;
            --danger: #e74c3c;
            --light: #ecf0f1;
            --dark: #34495e;
        }
        
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background-color: #f5f7fa;
            color: var(--dark);
            line-height: 1.6;
            padding: 20px;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
        }
        
        header {
            text-align: center;
            margin-bottom: 30px;
            padding: 20px;
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        
        h1 {
            color: var(--primary);
            margin-bottom: 10px;
        }
        
        .warning {
            background-color: #fff3cd;
            border: 1px solid #ffeaa7;
            color: #856404;
            padding: 15px;
            border-radius: 5px;
            margin: 15px 0;
            font-weight: bold;
        }
        
        .flow-container {
            display: flex;
            flex-direction: column;
            gap: 20px;
        }
        
        .step {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            overflow: hidden;
        }
        
        .step-header {
            background-color: var(--primary);
            color: white;
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            cursor: pointer;
        }
        
        .step-header h2 {
            font-size: 1.2rem;
            margin: 0;
        }
        
        .step-content {
            padding: 20px;
            display: none;
        }
        
        .step.active .step-content {
            display: block;
        }
        
        .step-number {
            background-color: var(--secondary);
            color: white;
            width: 30px;
            height: 30px;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-weight: bold;
        }
        
        .entity {
            margin: 15px 0;
            padding: 15px;
            border-radius: 5px;
            background-color: var(--light);
        }
        
        .entity h3 {
            margin-bottom: 10px;
            color: var(--primary);
        }
        
        .code-block {
            background-color: #2d3748;
            color: #e2e8f0;
            padding: 15px;
            border-radius: 5px;
            overflow-x: auto;
            margin: 10px 0;
            font-family: 'Courier New', monospace;
            font-size: 0.9rem;
        }
        
        .request-response {
            display: flex;
            gap: 20px;
            margin: 20px 0;
        }
        
        .request, .response {
            flex: 1;
        }
        
        .request h3, .response h3 {
            background-color: var(--secondary);
            color: white;
            padding: 10px;
            border-radius: 5px 5px 0 0;
        }
        
        .request-content, .response-content {
            background-color: white;
            border: 1px solid #ddd;
            border-top: none;
            padding: 15px;
            border-radius: 0 0 5px 5px;
        }
        
        .btn {
            display: inline-block;
            background-color: var(--secondary);
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
            transition: background-color 0.3s;
            margin: 5px;
        }
        
        .btn:hover {
            background-color: #2980b9;
        }
        
        .btn-success {
            background-color: var(--success);
        }
        
        .btn-success:hover {
            background-color: #219653;
        }
        
        .btn-warning {
            background-color: var(--warning);
        }
        
        .btn-warning:hover {
            background-color: #e67e22;
        }
        
        .btn-danger {
            background-color: var(--danger);
        }
        
        .btn-danger:hover {
            background-color: #c0392b;
        }
        
        .token-display {
            background-color: #f8f9fa;
            border: 1px solid #dee2e6;
            border-radius: 5px;
            padding: 15px;
            margin: 15px 0;
            word-break: break-all;
        }
        
        .token-label {
            font-weight: bold;
            color: var(--primary);
        }
        
        .flow-diagram {
            text-align: center;
            margin: 30px 0;
        }
        
        .flow-diagram img {
            max-width: 100%;
            border: 1px solid #ddd;
            border-radius: 5px;
        }
        
        .demo-controls {
            text-align: center;
            margin: 20px 0;
        }
        
        .status {
            padding: 10px;
            border-radius: 5px;
            margin: 10px 0;
            text-align: center;
            font-weight: bold;
        }
        
        .status-success {
            background-color: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }
        
        .status-error {
            background-color: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }
        
        .status-info {
            background-color: #d1ecf1;
            color: #0c5460;
            border: 1px solid #bee5eb;
        }
        
        .form-group {
            margin-bottom: 15px;
        }
        
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        
        input[type="text"], input[type="password"] {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
        }
        
        @media (max-width: 768px) {
            .request-response {
                flex-direction: column;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>OAuth 2.0 Authorization Code Grant Flow Demo</h1>
            <p>This demo visualizes the complete OAuth 2.0 Authorization Code Grant Flow with all steps</p>
        </header>
        
        <div class="warning">
            <strong>Important Note:</strong> This demo client is for demonstration purposes only. All the mandatory validations and recommended security precautions of a production-ready OAuth 2.0 client library are missing here. Do NOT use any code of this client for production!
        </div>
        
        <div class="flow-diagram">
            <h2>OAuth 2.0 Authorization Code Grant Flow</h2>
            <div style="background-color: white; padding: 20px; border-radius: 8px; box-shadow: 0 2px 10px rgba(0,0,0,0.1);">
                <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
                    <div style="text-align: center;">
                        <div style="background-color: #3498db; color: white; padding: 10px; border-radius: 5px;">Resource Owner (User)</div>
                    </div>
                    <div style="text-align: center;">
                        <div style="background-color: #2ecc71; color: white; padding: 10px; border-radius: 5px;">Client Application</div>
                    </div>
                    <div style="text-align: center;">
                        <div style="background-color: #e74c3c; color: white; padding: 10px; border-radius: 5px;">Authorization Server</div>
                    </div>
                </div>
                
                <div style="display: flex; flex-direction: column; gap: 15px;">
                    <div style="display: flex; justify-content: space-between; align-items: center;">
                        <div style="width: 30%; text-align: right;">User accesses client app</div>
                        <div style="width: 5%; text-align: center;">→</div>
                        <div style="width: 30%; text-align: left;">Client redirects to Auth Server</div>
                        <div style="width: 5%; text-align: center;">→</div>
                        <div style="width: 30%; text-align: right;">Auth Server shows login & consent</div>
                    </div>
                    
                    <div style="display: flex; justify-content: space-between; align-items: center;">
                        <div style="width: 30%; text-align: right;">User authenticates & consents</div>
                        <div style="width: 5%; text-align: center;">→</div>
                        <div style="width: 30%; text-align: left;">Auth Server redirects with auth code</div>
                        <div style="width: 5%; text-align: center;">→</div>
                        <div style="width: 30%; text-align: right;">Client receives auth code</div>
                    </div>
                    
                    <div style="display: flex; justify-content: space-between; align-items: center;">
                        <div style="width: 30%; text-align: right;"></div>
                        <div style="width: 5%; text-align: center;">→</div>
                        <div style="width: 30%; text-align: left;">Client exchanges code for tokens</div>
                        <div style="width: 5%; text-align: center;">→</div>
                        <div style="width: 30%; text-align: right;">Auth Server returns tokens</div>
                    </div>
                    
                    <div style="display: flex; justify-content: space-between; align-items: center;">
                        <div style="width: 30%; text-align: right;"></div>
                        <div style="width: 5%; text-align: center;">→</div>
                        <div style="width: 30%; text-align: left;">Client uses access token</div>
                        <div style="width: 5%; text-align: center;">→</div>
                        <div style="width: 30%; text-align: right;">Access protected resources</div>
                    </div>
                </div>
            </div>
        </div>
        
        <div class="demo-controls">
            <button id="startDemo" class="btn btn-success">Start Demo Flow</button>
            <button id="resetDemo" class="btn btn-danger">Reset Demo</button>
        </div>
        
        <div id="statusMessage" class="status status-info" style="display: none;">
            Click "Start Demo Flow" to begin the OAuth 2.0 Authorization Code Grant Flow demonstration.
        </div>
        
        <div class="flow-container">
            <!-- Step 1: Authorization Request -->
            <div class="step" id="step1">
                <div class="step-header">
                    <h2>Step 1: Authorization Request</h2>
                    <div class="step-number">1</div>
                </div>
                <div class="step-content">
                    <p>The client application redirects the user to the authorization server with the following parameters:</p>
                    
                    <div class="entity">
                        <h3>Client Application</h3>
                        <p>Initiates the OAuth flow by redirecting the user to the authorization server.</p>
                    </div>
                    
                    <div class="request-response">
                        <div class="request">
                            <h3>Authorization Request</h3>
                            <div class="request-content">
                                <p><strong>Endpoint:</strong> <code id="authEndpoint">https://auth-server.example.com/authorize</code></p>
                                <p><strong>HTTP Method:</strong> GET</p>
                                <div class="code-block">
                                
	GET /authorize?response_type=code
	&client_id=example_client_id
	&redirect_uri=https://client.example.com/callback
	&scope=read%20write
	&state=random_state_string HTTP/1.1
	Host: auth-server.example.com
                                </div>
                            </div>
                        </div>
                        
                        <div class="response">
                            <h3>User Interaction</h3>
                            <div class="request-content">
                                <p>The authorization server presents a login form and consent screen to the user.</p>
                                
                                <div class="form-group">
                                    <label for="username">Username:</label>
                                    <input type="text" id="username" value="demo_user">
                                </div>
                                
                                <div class="form-group">
                                    <label for="password">Password:</label>
                                    <input type="password" id="password" value="demo_password">
                                </div>
                                
                                <div class="form-group">
                                    <label>Requested Permissions:</label>
                                    <div>
                                        <input type="checkbox" id="scopeRead" checked disabled> Read access<br>
                                        <input type="checkbox" id="scopeWrite" checked disabled> Write access
                                    </div>
                                </div>
                                
                                <button id="approveAuth" class="btn btn-success" disabled>Approve</button>
                                <button id="denyAuth" class="btn btn-danger" disabled>Deny</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Step 2: Authorization Response -->
            <div class="step" id="step2">
                <div class="step-header">
                    <h2>Step 2: Authorization Response</h2>
                    <div class="step-number">2</div>
                </div>
                <div class="step-content">
                    <p>After the user approves the request, the authorization server redirects back to the client with an authorization code.</p>
                    
                    <div class="entity">
                        <h3>Authorization Server</h3>
                        <p>Processes the user's approval and redirects back to the client with an authorization code.</p>
                    </div>
                    
                    <div class="request-response">
                        <div class="request">
                            <h3>Redirect to Client</h3>
                            <div class="request-content">
                                <p><strong>HTTP Status:</strong> 302 Found</p>
                                <p><strong>Location Header:</strong></p>
                                <div class="code-block" id="redirectLocation">
	https://client.example.com/callback?
	code=AUTH_CODE_12345
	&state=random_state_string
                                </div>
                            </div>
                        </div>
                        
                        <div class="response">
                            <h3>Client Receives Authorization Code</h3>
                            <div class="request-content">
                                <p>The client extracts the authorization code from the URL parameters.</p>
                                
                                <div class="token-display">
                                    <span class="token-label">Authorization Code:</span>
                                    <span id="authCodeDisplay">Not received yet</span>
                                </div>
                                
                                <div class="token-display">
                                    <span class="token-label">State:</span>
                                    <span id="stateDisplay">Not received yet</span>
                                </div>
                                
                                <p><em>The client should validate that the state parameter matches the one sent in the authorization request to prevent CSRF attacks.</em></p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Step 3: Token Request -->
            <div class="step" id="step3">
                <div class="step-header">
                    <h2>Step 3: Token Request</h2>
                    <div class="step-number">3</div>
                </div>
                <div class="step-content">
                    <p>The client exchanges the authorization code for an access token by making a server-to-server request to the token endpoint.</p>
                    
                    <div class="entity">
                        <h3>Client Application</h3>
                        <p>Exchanges the authorization code for tokens at the token endpoint.</p>
                    </div>
                    
                    <div class="request-response">
                        <div class="request">
                            <h3>Token Request</h3>
                            <div class="request-content">
                                <p><strong>Endpoint:</strong> <code>https://auth-server.example.com/token</code></p>
                                <p><strong>HTTP Method:</strong> POST</p>
                                <div class="code-block">
	POST /token HTTP/1.1
	Host: auth-server.example.com
	Content-Type: application/x-www-form-urlencoded

	grant_type=authorization_code
	&code=AUTH_CODE_12345
	&redirect_uri=https://client.example.com/callback
	&client_id=example_client_id
	&client_secret=example_client_secret
                                </div>
                            </div>
                        </div>
                        
                        <div class="response">
                            <h3>Token Response</h3>
                            <div class="request-content">
                                <p>The authorization server responds with an access token and optionally a refresh token.</p>
                                
                                <div class="code-block" id="tokenResponse">
	{
	  "access_token": "ACCESS_TOKEN_XYZ",
	  "token_type": "Bearer",
	  "expires_in": 3600,
	  "refresh_token": "REFRESH_TOKEN_ABC",
	  "scope": "read write"
	}
                                </div>
                                
                                <div class="token-display">
                                    <span class="token-label">Access Token:</span>
                                    <span id="accessTokenDisplay">Not received yet</span>
                                </div>
                                
                                <div class="token-display">
                                    <span class="token-label">Refresh Token:</span>
                                    <span id="refreshTokenDisplay">Not received yet</span>
                                </div>
                                
                                <button id="exchangeToken" class="btn btn-success" disabled>Exchange Code for Token</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Step 4: Using the Access Token -->
            <div class="step" id="step4">
                <div class="step-header">
                    <h2>Step 4: Using the Access Token</h2>
                    <div class="step-number">4</div>
                </div>
                <div class="step-content">
                    <p>The client can now use the access token to make requests to the resource server.</p>
                    
                    <div class="entity">
                        <h3>Resource Server</h3>
                        <p>Validates the access token and returns the requested resources.</p>
                    </div>
                    
                    <div class="request-response">
                        <div class="request">
                            <h3>API Request with Access Token</h3>
                            <div class="request-content">
                                <p><strong>Endpoint:</strong> <code>https://api.example.com/user/profile</code></p>
                                <p><strong>HTTP Method:</strong> GET</p>
                                <div class="code-block">
	GET /user/profile HTTP/1.1
	Host: api.example.com
	Authorization: Bearer ACCESS_TOKEN_XYZ
                                </div>
                                
                                <button id="makeApiCall" class="btn btn-success" disabled>Make API Request</button>
                            </div>
                        </div>
                        
                        <div class="response">
                            <h3>API Response</h3>
                            <div class="request-content">
                                <p>The resource server validates the token and returns the requested data.</p>
                                
                                <div class="code-block" id="apiResponse">
	{
	  "user_id": "12345",
	  "username": "demo_user",
	  "email": "user@example.com",
	  "profile_data": {
	    "name": "Demo User",
	    "preferences": {}
	  }
	}
                                </div>
                                
                                <div id="apiCallStatus" class="status" style="display: none;"></div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Step 5: Token Introspection and Refresh -->
            <div class="step" id="step5">
                <div class="step-header">
                    <h2>Step 5: Token Introspection and Refresh</h2>
                    <div class="step-number">5</div>
                </div>
                <div class="step-content">
                    <p>Additional operations: check token validity and refresh expired tokens.</p>
                    
                    <div class="request-response">
                        <div class="request">
                            <h3>Token Introspection</h3>
                            <div class="request-content">
                                <p>Check if a token is still valid and get information about it.</p>
                                
                                <div class="code-block">
	POST /introspect HTTP/1.1
	Host: auth-server.example.com
	Content-Type: application/x-www-form-urlencoded

	token=ACCESS_TOKEN_XYZ
	&token_type_hint=access_token
	&client_id=example_client_id
	&client_secret=example_client_secret
                                </div>
                                
                                <button id="introspectToken" class="btn btn-warning" disabled>Introspect Token</button>
                            </div>
                        </div>
                        
                        <div class="response">
                            <h3>Introspection Response</h3>
                            <div class="request-content">
                                <div class="code-block" id="introspectionResponse">
	{
	  "active": true,
	  "scope": "read write",
	  "client_id": "example_client_id",
	  "username": "demo_user",
	  "token_type": "access_token",
	  "exp": 1620000000,
	  "iat": 1619996400
	}
                                </div>
                            </div>
                        </div>
                    </div>
                    
                    <div class="request-response" style="margin-top: 20px;">
                        <div class="request">
                            <h3>Token Refresh</h3>
                            <div class="request-content">
                                <p>Use the refresh token to get a new access token without user interaction.</p>
                                
                                <div class="code-block">
	POST /token HTTP/1.1
	Host: auth-server.example.com
	Content-Type: application/x-www-form-urlencoded

	grant_type=refresh_token
	&refresh_token=REFRESH_TOKEN_ABC
	&client_id=example_client_id
	&client_secret=example_client_secret
                                </div>
                                
                                <button id="refreshTokenBtn" class="btn btn-warning" disabled>Refresh Token</button>
                            </div>
                        </div>
                        
                        <div class="response">
                            <h3>Refresh Response</h3>
                            <div class="request-content">
                                <div class="code-block" id="refreshResponse">
	{
	  "access_token": "NEW_ACCESS_TOKEN_XYZ",
	  "token_type": "Bearer",
	  "expires_in": 3600,
	  "refresh_token": "NEW_REFRESH_TOKEN_ABC",
	  "scope": "read write"
	}
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            // Demo state
            const demoState = {
                currentStep: 0,
                authCode: null,
                accessToken: null,
                refreshToken: null,
                state: 'demo_state_' + Math.random().toString(36).substring(2, 15)
            };
            
            // DOM elements
            const startDemoBtn = document.getElementById('startDemo');
            const resetDemoBtn = document.getElementById('resetDemo');
            const approveAuthBtn = document.getElementById('approveAuth');
            const denyAuthBtn = document.getElementById('denyAuth');
            const exchangeTokenBtn = document.getElementById('exchangeToken');
            const makeApiCallBtn = document.getElementById('makeApiCall');
            const introspectTokenBtn = document.getElementById('introspectToken');
            const refreshTokenBtn = document.getElementById('refreshTokenBtn');
            const statusMessage = document.getElementById('statusMessage');
            
            // Initialize the demo
            function initDemo() {
                // Reset all steps
                document.querySelectorAll('.step').forEach(step => {
                    step.classList.remove('active');
                });
                
                // Reset all displays
                document.getElementById('authCodeDisplay').textContent = 'Not received yet';
                document.getElementById('stateDisplay').textContent = 'Not received yet';
                document.getElementById('accessTokenDisplay').textContent = 'Not received yet';
                document.getElementById('refreshTokenDisplay').textContent = 'Not received yet';
                
                // Reset demo state
                demoState.currentStep = 0;
                demoState.authCode = null;
                demoState.accessToken = null;
                demoState.refreshToken = null;
                
                // Update redirect location with new state
                document.getElementById('redirectLocation').textContent = 
                    `https://client.example.com/callback?\ncode=AUTH_CODE_${Math.random().toString(36).substring(2, 10).toUpperCase()}\n&state=${demoState.state}`;
                
                // Disable all buttons except start
                setButtonsState(false);
                startDemoBtn.disabled = false;
                
                // Show initial status message
                statusMessage.textContent = 'Click "Start Demo Flow" to begin the OAuth 2.0 Authorization Code Grant Flow demonstration.';
                statusMessage.className = 'status status-info';
                statusMessage.style.display = 'block';
            }
            
            // Set the state of interactive buttons
            function setButtonsState(enabled) {
                approveAuthBtn.disabled = !enabled;
                denyAuthBtn.disabled = !enabled;
                exchangeTokenBtn.disabled = !enabled;
                makeApiCallBtn.disabled = !enabled;
                introspectTokenBtn.disabled = !enabled;
                refreshTokenBtn.disabled = !enabled;
            }
            
            // Advance to the next step
            function nextStep() {
                demoState.currentStep++;
                
                // Activate the current step
                document.querySelectorAll('.step').forEach((step, index) => {
                    if (index < demoState.currentStep) {
                        step.classList.add('active');
                    } else {
                        step.classList.remove('active');
                    }
                });
                
                // Scroll to the current step
                if (demoState.currentStep > 0) {
                    document.getElementById(`step${demoState.currentStep}`).scrollIntoView({ behavior: 'smooth' });
                }
            }
            
            // Start the demo
            startDemoBtn.addEventListener('click', function() {
                initDemo();
                nextStep();
                setButtonsState(true);
                
                statusMessage.textContent = 'Step 1: Authorization Request - The client is redirecting the user to the authorization server.';
                statusMessage.className = 'status status-info';
            });
            
            // Reset the demo
            resetDemoBtn.addEventListener('click', initDemo);
            
            // User approves the authorization request
            approveAuthBtn.addEventListener('click', function() {
                // Generate a random authorization code
                demoState.authCode = 'AUTH_CODE_' + Math.random().toString(36).substring(2, 10).toUpperCase();
                
                // Update the display
                document.getElementById('authCodeDisplay').textContent = demoState.authCode;
                document.getElementById('stateDisplay').textContent = demoState.state;
                
                // Update the token request with the actual auth code
                const tokenRequestElement = document.querySelector('#step3 .code-block');
                tokenRequestElement.textContent = tokenRequestElement.textContent.replace('AUTH_CODE_12345', demoState.authCode);
                
                nextStep();
                
                statusMessage.textContent = 'Step 2: Authorization Response - The authorization server has redirected back to the client with an authorization code.';
                statusMessage.className = 'status status-success';
            });
            
            // User denies the authorization request
            denyAuthBtn.addEventListener('click', function() {
                statusMessage.textContent = 'Authorization denied by user. The OAuth flow cannot continue.';
                statusMessage.className = 'status status-error';
                
                // Disable further steps
                setButtonsState(false);
            });
            
            // Exchange authorization code for tokens
            exchangeTokenBtn.addEventListener('click', function() {
                // Generate tokens
                demoState.accessToken = 'ACCESS_TOKEN_' + Math.random().toString(36).substring(2, 15).toUpperCase();
                demoState.refreshToken = 'REFRESH_TOKEN_' + Math.random().toString(36).substring(2, 15).toUpperCase();
                
                // Update displays
                document.getElementById('accessTokenDisplay').textContent = demoState.accessToken;
                document.getElementById('refreshTokenDisplay').textContent = demoState.refreshToken;
                
                // Update the token response
                const tokenResponseElement = document.getElementById('tokenResponse');
                tokenResponseElement.textContent = tokenResponseElement.textContent
                    .replace('ACCESS_TOKEN_XYZ', demoState.accessToken)
                    .replace('REFRESH_TOKEN_ABC', demoState.refreshToken);
                
                // Update API request with actual token
                const apiRequestElement = document.querySelector('#step4 .code-block');
                apiRequestElement.textContent = apiRequestElement.textContent.replace('ACCESS_TOKEN_XYZ', demoState.accessToken);
                
                nextStep();
                
                statusMessage.textContent = 'Step 3: Token Request - The client has exchanged the authorization code for an access token and refresh token.';
                statusMessage.className = 'status status-success';
            });
            
            // Make API call with access token
            makeApiCallBtn.addEventListener('click', function() {
                const apiCallStatus = document.getElementById('apiCallStatus');
                apiCallStatus.textContent = 'API call successful! The access token was accepted by the resource server.';
                apiCallStatus.className = 'status status-success';
                apiCallStatus.style.display = 'block';
                
                nextStep();
                
                statusMessage.textContent = 'Step 4: Using the Access Token - The client successfully accessed protected resources using the access token.';
                statusMessage.className = 'status status-success';
            });
            
            // Introspect token
            introspectTokenBtn.addEventListener('click', function() {
                const introspectionResponse = document.getElementById('introspectionResponse');
                introspectionResponse.textContent = JSON.stringify({
                    active: true,
                    scope: "read write",
                    client_id: "example_client_id",
                    username: "demo_user",
                    token_type: "access_token",
                    exp: Math.floor(Date.now() / 1000) + 3600,
                    iat: Math.floor(Date.now() / 1000)
                }, null, 2);
                
                statusMessage.textContent = 'Token introspection completed - The token is valid and active.';
                statusMessage.className = 'status status-info';
            });
            
            // Refresh token
            refreshTokenBtn.addEventListener('click', function() {
                // Generate new tokens
                const newAccessToken = 'NEW_ACCESS_TOKEN_' + Math.random().toString(36).substring(2, 15).toUpperCase();
                const newRefreshToken = 'NEW_REFRESH_TOKEN_' + Math.random().toString(36).substring(2, 15).toUpperCase();
                
                // Update the refresh response
                const refreshResponse = document.getElementById('refreshResponse');
                refreshResponse.textContent = refreshResponse.textContent
                    .replace('NEW_ACCESS_TOKEN_XYZ', newAccessToken)
                    .replace('NEW_REFRESH_TOKEN_ABC', newRefreshToken);
                
                // Update displays with new tokens
                document.getElementById('accessTokenDisplay').textContent = newAccessToken;
                document.getElementById('refreshTokenDisplay').textContent = newRefreshToken;
                
                // Update API request with new token
                const apiRequestElement = document.querySelector('#step4 .code-block');
                apiRequestElement.textContent = apiRequestElement.textContent.replace(demoState.accessToken, newAccessToken);
                
                // Update demo state
                demoState.accessToken = newAccessToken;
                demoState.refreshToken = newRefreshToken;
                
                statusMessage.textContent = 'Token refresh completed - New access token and refresh token have been issued.';
                statusMessage.className = 'status status-success';
            });
            
            // Initialize the demo on page load
            initDemo();
        });
    </script>
</body>
</html>








<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>OAuth 2.0 - What Actually Happens</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: #f5f7fa;
            color: #2c3e50;
            line-height: 1.6;
            padding: 20px;
        }
        
        .container {
            max-width: 1000px;
            margin: 0 auto;
            background: white;
            border-radius: 10px;
            padding: 30px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }
        
        h1 {
            text-align: center;
            margin-bottom: 10px;
            color: #2c3e50;
        }
        
        .subtitle {
            text-align: center;
            color: #7f8c8d;
            margin-bottom: 30px;
            font-size: 1.1rem;
        }
        
        .reality-check {
            background: #fff3cd;
            border: 1px solid #ffeaa7;
            border-left: 4px solid #f39c12;
            padding: 20px;
            margin: 20px 0;
            border-radius: 5px;
        }
        
        .flow-entities {
            display: flex;
            justify-content: space-between;
            margin: 40px 0;
            text-align: center;
        }
        
        .entity {
            flex: 1;
            padding: 20px;
            margin: 0 10px;
            background: white;
            border-radius: 8px;
            box-shadow: 0 3px 10px rgba(0,0,0,0.1);
        }
        
        .entity h3 {
            margin-bottom: 10px;
            color: #2c3e50;
        }
        
        .user { border-top: 4px solid #3498db; }
        .browser { border-top: 4px solid #2ecc71; }
        .client { border-top: 4px solid #e74c3c; }
        .auth-server { border-top: 4px solid #9b59b6; }
        
        .flow-steps {
            display: flex;
            flex-direction: column;
            gap: 25px;
            margin: 30px 0;
        }
        
        .step {
            display: flex;
            align-items: center;
            padding: 20px;
            background: #f8f9fa;
            border-radius: 8px;
            border-left: 4px solid #3498db;
        }
        
        .step-number {
            background: #3498db;
            color: white;
            width: 35px;
            height: 35px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            margin-right: 20px;
            flex-shrink: 0;
        }
        
        .step-content {
            flex: 1;
        }
        
        .step-title {
            font-weight: bold;
            margin-bottom: 5px;
            color: #2c3e50;
        }
        
        .step-description {
            color: #7f8c8d;
            font-size: 0.95rem;
        }
        
        .technical-details {
            background: #2c3e50;
            color: white;
            padding: 15px;
            border-radius: 5px;
            margin-top: 10px;
            font-family: 'Courier New', monospace;
            font-size: 0.9rem;
        }
        
        .key-points {
            background: #e8f4fd;
            border-radius: 8px;
            padding: 25px;
            margin: 30px 0;
        }
        
        .key-points h3 {
            margin-bottom: 15px;
            color: #2c3e50;
        }
        
        .key-points ul {
            list-style-position: inside;
        }
        
        .key-points li {
            margin-bottom: 10px;
            padding-left: 10px;
        }
        
        .confusion-clarification {
            background: #fdede8;
            border-radius: 8px;
            padding: 25px;
            margin: 30px 0;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>OAuth 2.0 Authorization Code Flow</h1>
        <p class="subtitle">What actually happens behind the "Login with X" button</p>
        
        <div class="reality-check">
            <strong>Common Confusion:</strong> Most diagrams oversimplify by showing direct communication between User and Authorization Server, but in reality, everything happens through the user's browser.
        </div>

        <div class="flow-entities">
            <div class="entity user">
                <h3>👤 User</h3>
                <p>Wants to access an app</p>
            </div>
            <div class="entity browser">
                <h3>🌐 User's Browser</h3>
                <p>The actual messenger</p>
            </div>
            <div class="entity client">
                <h3>💻 Client App</h3>
                <p>Wants user data</p>
            </div>
            <div class="entity auth-server">
                <h3>🔐 Auth Server</h3>
                <p>Holds user data</p>
            </div>
        </div>

        <div class="flow-steps">
            <!-- Step 1 -->
            <div class="step">
                <div class="step-number">1</div>
                <div class="step-content">
                    <div class="step-title">User clicks "Login"</div>
                    <div class="step-description">
                        User visits your app and clicks the login button
                    </div>
                </div>
            </div>
            
            <!-- Step 2 -->
            <div class="step">
                <div class="step-number">2</div>
                <div class="step-content">
                    <div class="step-title">Browser redirects to Auth Server</div>
                    <div class="step-description">
                        Your app returns an HTTP redirect to the authorization endpoint
                        <div class="technical-details">
                            HTTP 302 Found<br>
                            Location: https://auth.server/authorize?response_type=code&client_id=123&redirect_uri=...
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Step 3 -->
            <div class="step">
                <div class="step-number">3</div>
                <div class="step-content">
                    <div class="step-title">Auth Server shows login page</div>
                    <div class="step-description">
                        User enters credentials directly on the auth server's domain
                    </div>
                </div>
            </div>
            
            <!-- Step 4 -->
            <div class="step">
                <div class="step-number">4</div>
                <div class="step-content">
                    <div class="step-title">Auth Server redirects back with code</div>
                    <div class="step-description">
                        After successful login, auth server redirects browser back to your app with an authorization code
                        <div class="technical-details">
                            HTTP 302 Found<br>
                            Location: https://yourapp.com/callback?code=AUTH_CODE_HERE
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Step 5 -->
            <div class="step">
                <div class="step-number">5</div>
                <div class="step-content">
                    <div class="step-title">Server exchanges code for token</div>
                    <div class="step-description">
                        Your backend makes a direct server-to-server call to exchange the code for an access token
                        <div class="technical-details">
                            POST https://auth.server/token<br>
                            grant_type=authorization_code&code=AUTH_CODE_HERE&client_secret=...
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Step 6 -->
            <div class="step">
                <div class="step-number">6</div>
                <div class="step-content">
                    <div class="step-title">Use access token</div>
                    <div class="step-description">
                        Your app uses the access token to make API calls on behalf of the user
                    </div>
                </div>
            </div>
        </div>

        <div class="key-points">
            <h3>What Most People Get Wrong:</h3>
            <ul>
                <li><strong>The browser does all the redirecting</strong> - not the servers talking directly</li>
                <li><strong>User never leaves their browser</strong> - they're just visiting different URLs</li>
                <li><strong>Only Step 5 is server-to-server</strong> - everything else is browser redirects</li>
                <li><strong>The "code" is just a temporary token</strong> - it's useless without the client secret</li>
            </ul>
        </div>

        <div class="confusion-clarification">
            <h3>Why This Matters:</h3>
            <p>Understanding that the browser is the messenger explains:</p>
            <ul>
                <li>Why OAuth works for web apps (the browser can redirect)</li>
                <li>Why native/mobile apps need special handling (they don't have a browser's redirect capability)</li>
                <li>Where security vulnerabilities actually occur (URL manipulation, CSRF, etc.)</li>
                <li>Why PKCE was added for public clients (to replace the client secret)</li>
            </ul>
        </div>
    </div>
</body>
</html>