<script>
    let username = '';
    let password = '';
    let isAuthenticated = false;
    let errorMessage = '';

    import { onMount } from "svelte";
 

    function logout() {
      // Clear the JWT token from localStorage or a cookie
      localStorage.removeItem('token');
      // Set isAuthenticated to false
      isAuthenticated = false;
    }
  
    async function login() {
      try {
        // Make a POST request to your server to authenticate the user
        const response = await fetch('/api/login', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({ username, password }),
        });
  
        if (response.ok) {
          // Assuming your server returns a JWT token upon successful login
          const { token } = await response.json();
  
          // Store the token in localStorage or a cookie for future requests
          localStorage.setItem('token', token);
  
          // Set isAuthenticated to true and clear error message
          isAuthenticated = true;
          errorMessage = '';
        } else {
          // Handle login failure
          isAuthenticated = false;
          errorMessage = 'Login failed. Please check your credentials.';
        }
      } catch (error) {
        console.error('Error:', error);
        isAuthenticated = false;
        errorMessage = 'An error occurred during login.';
      }
    }
  
    // Check if a JWT token is stored in localStorage on component load
    onMount(() => {
      const token = localStorage.getItem('token');
      if (token) {
        // You may want to verify the token's validity here
        isAuthenticated = true;
      }
    });
  </script>
  
  {#if isAuthenticated}
    <h1>Welcome to the protected page!</h1>
    <button on:click={logout}>Logout</button>
  {:else}
    <h1>Login</h1>
    <form on:submit={login}>
      <input type="text" bind:value={username} placeholder="Username" />
      <input type="password" bind:value={password} placeholder="Password" />
      <button type="submit">Login</button>
    </form>
    <p>{errorMessage}</p>
  {/if}
  
 
 