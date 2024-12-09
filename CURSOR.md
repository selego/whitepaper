You are an expert in JavaScript, ReactJS, Node.js, MongoDB, mongoose, and TailwindCSS.

Code Style and Structure
- Write Clear and Functional JavaScript: Use functional programming patterns, React hooks (only useState and useEffect), and modular, reusable code structures. Avoid object-oriented programming when possible.
- Focus on Simplicity (KIS): Break complex logic into smaller, readable pieces. Use early returns and avoid unnecessary abstractions.
- Encourage Consistent Practices: Write code that looks authored by a single person across projects. Prioritize clarity, maintainability, and adherence to shared conventions.
- Descriptive Naming: Use meaningful and precise names for variables and functions (e.g., fetchUsers, isLoading, handleDelete).
- Scalable File Structures: Organize projects into predictable folders like components, scenes, services, and utils for a consistent architecture.
- Modern Frontend Standards: Use Tailwind CSS for styling and React functional components for UI. Default to responsive design using grids and embrace modern layout principles.
- Backend Consistency: Ensure APIs have a single responsibility per endpoint. Use flat data structures and standardize API responses in {ok, data, error} format.
- Optimize Collaboration: Keep pull requests (PRs) small and focused to ensure seamless deployments with minimal conflicts.
- Leverage Monorepo: Use a single repository for related codebases to improve code reuse and reduce overhead.
- Documentation and Automation: Maintain comprehensive documentation and use automation tools for testing, deployment, and repetitive tasks.
- Iterate Quickly During MVP Development: Prioritize speed over perfection in early stages. Hardcode non-sensitive credentials to enable rapid prototyping.

Naming Conventions
- Directories: Use lowercase with dashes for folder names (e.g., components/user-profile, services/auth-service).
- Files: Align file names with their folders using lowercase with dashes (e.g., auth-service.js in services/auth-service).
- Component Exports: Use named exports for components and helpers. Prefer export const UserProfile = () => {} over export default UserProfile.
- Variables and Functions: Use camelCase for naming variables and functions (e.g., fetchData, isLoading, handleDelete).
- Constants: Use UPPER_CASE_WITH_UNDERSCORES for constants (e.g., API_BASE_URL, DEFAULT_PAGE_SIZE).
- React Components: Use PascalCase for component names (e.g., UserProfile, LoginButton).
- CSS/Tailwind Utilities: Follow kebab-case for Tailwind utility classes (e.g., text-gray-700, max-w-7xl).
- Route Parameters: Use snake_case for dynamic route parameters (e.g., /users/:user_id).

Syntax and Formatting
- Use the function keyword for pure functions.
- Avoid unnecessary curly braces in conditionals; prefer concise syntax for simple statements.
- Write declarative JSX for improved readability.
- Use Prettier for consistent code formatting.

UI and Styling
- Consistency with Tailwind CSS: Utilize Tailwind for styling across projects to ensure design uniformity.
- Declarative JSX: Keep JSX declarative to reflect the UI's intent and structure. Avoid excessive conditional logic in render methods.
- Custom UI Components: Avoid external packages where possible; leverage custom components for shared UI elements.

Selego Performance Optimization
- Use useState and useEffect for most state and lifecycle management. Avoid complex abstractions like reducers or contexts unless absolutely necessary for global state management.

Navigation
- React Router for Web: Use react-router-dom for navigation and follow best practices for managing stacks, tabs, and routes.

Example:
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';

const App = () => (
  <Router>
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
    </Routes>
  </Router>
);

- Dynamic Routes: Define parameterized routes for entity-specific pages (e.g., /user/:id).
- Clean URL Structures: Use clear and logical URL paths, such as:
/dashboard/settings
/user/:id
/search?query=term

- Deep Linking: Configure deep linking for seamless integration with external apps and user engagement.

Tab and Drawer Navigation:
- Tabs: Use for primary, frequently accessed content.
- Drawers: Reserve for secondary or less critical screens.

State Management
- Simplified State Management: Use useState and useEffect for local state management. Avoid Redux or Zustand unless the application is large-scale or highly complex.
- React Context for Global State: Use React Context for lightweight global state, such as theme, authentication, or app-wide settings.

Example:
const AuthContext = React.createContext();

const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);

  return (
    <AuthContext.Provider value={{ user, setUser }}>
      {children}
    </AuthContext.Provider>
  );
};

- Efficient Data Fetching: Centralize API calls using reusable services.

Example:
const fetchEvents = async () => {
  try {
    const { data, ok, total } = await api.post("/event/search");
    if (!ok) return toast.error(data.message);
    setEvents(data);
    setTotal(total);
  } catch (e) {
    console.error(e);
  } finally {
    setLoading(false);
  }
};

useEffect(() => {
  fetchEvents();
}, []);


Error Handling and Validation
- Custom Validation Functions: Use lightweight, custom functions for runtime checks instead of external libraries like Zod, Joi, etc.

Example:
const validateEmail = (email) => {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return emailRegex.test(email);
};

- Early Error Handling: Address errors early in functions with early returns to simplify code structure.

Example:
const processOrder = (order) => {
  if (!order) return console.error('Invalid order.');
  if (!order.items.length) return console.error('Order is empty.');

  console.log('Processing order:', order);
};

- Avoid Nested Conditionals: Simplify logic using the if-return pattern.

Example:
const saveData = (data) => {
  if (!data) return console.error('No data provided');
  console.log('Data saved:', data);
};