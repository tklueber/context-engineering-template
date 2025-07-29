### **Enhanced Workflow for the Angular Project**

#### 🔄 Project Awareness & Context

- **Always read `INITIAL.md`** at the start of a new conversation to understand the project's architecture, goals, style, and constraints.
- **Use consistent naming conventions, file structure, and architecture patterns** as described in `INITIAL.md` (e.g., `core/services`, `features/...`, `shared/...`).
- **Check for project dependencies and configurations** before implementing new features (package.json, angular.json, tsconfig.json).

#### 🧱 Code Structure & Modularity

- **Never create a file longer than 500 lines of code.** If a file approaches this limit, refactor by splitting it into smaller components or services.
- **Organize code into clearly separated modules**, grouped by feature or responsibility, as defined in our plan:
  - `core/services`: For core logic (State Management, Persistence).
  - `features/feature-name`: For UI components that implement a specific feature.
  - `shared/components`: For reusable UI components.
  - `models`: For TypeScript interfaces for data modeling.
- **Use `environment.ts` and `environment.prod.ts`** for environment-specific variables (e.g., API URLs or feature flags).
- **Feature modules under 250KB compressed** - implement lazy loading for larger features.

#### 🛡️ Security & Validation

- **Input Validation**: Always validate user inputs, especially file uploads (MIME type, size limits, file content validation).
- **CSP Compliance**: Ensure all dynamic content follows Content Security Policy guidelines.
- **XSS Prevention**: Sanitize user-generated content before display using Angular's DomSanitizer.
- **File Upload Security**:
  - Validate image files before storing in IndexedDB
  - Check MIME types: only allow `image/jpeg`, `image/png`, `image/webp`
  - Implement file size limits (max 10MB per file)
  - Validate file headers, not just extensions

```typescript
// Security validation pattern
validateImageFile(file: File): { valid: boolean; error?: string } {
  const allowedTypes = ['image/jpeg', 'image/png', 'image/webp'];
  const maxSize = 10 * 1024 * 1024; // 10MB
  
  if (!allowedTypes.includes(file.type)) {
    return { valid: false, error: 'Invalid file type. Only JPEG, PNG, and WebP allowed.' };
  }
  
  if (file.size > maxSize) {
    return { valid: false, error: 'File too large. Maximum size is 10MB.' };
  }
  
  return { valid: true };
}
```

#### ⚡ Performance Guidelines

- **Bundle Size**: Keep feature modules under 250KB compressed using lazy loading.
- **Change Detection**: Use `OnPush` strategy for performance-critical components.
- **IndexedDB Optimization**: Batch database operations, use transactions for multiple operations.
- **Image Optimization**: Resize uploaded images to max 512x512px, compress to <100KB before storage.
- **Virtual Scrolling**: Implement for lists with 100+ items using Angular CDK.
- **TrackBy Functions**: Always use trackBy for `*ngFor` with dynamic data.

```typescript
// Performance patterns
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush,
  // ...
})
export class OptimizedComponent {
  trackByPersonId(index: number, person: Person): string {
    return person.id;
  }
}
```

#### 🔄 Error Handling Patterns

- **Global Error Handler**: Implement Angular ErrorHandler for unhandled exceptions.
- **User-Friendly Messages**: Always show meaningful error messages to users, never expose technical details.
- **Retry Logic**: Implement exponential backoff for IndexedDB operations.
- **Fallback States**: Define fallback UI states for data loading failures.
- **Structured Error Responses**: Use consistent error response format across the application.

```typescript
// Error handling pattern
interface ApiResponse<T> {
  data?: T;
  error?: string;
  success: boolean;
  timestamp: Date;
}

// Service error handling
async savePersonSafely(person: Person): Promise<ApiResponse<Person>> {
  try {
    const saved = await this.persistenceService.addPerson(person);
    return { data: saved, success: true, timestamp: new Date() };
  } catch (error) {
    console.error('Failed to save person:', error);
    return { 
      error: 'Failed to save person. Please try again.', 
      success: false,
      timestamp: new Date()
    };
  }
}

// Global error handler
@Injectable()
export class GlobalErrorHandler implements ErrorHandler {
  handleError(error: any): void {
    console.error('Global error:', error);
    // Send to logging service, show user notification
  }
}
```

#### 📱 Accessibility (A11y)

- **ARIA Labels**: All interactive elements must have proper ARIA labels and roles.
- **Keyboard Navigation**: Ensure full keyboard accessibility with logical tab order.
- **Screen Reader Support**: Test with screen readers, provide descriptive alt texts.
- **Color Contrast**: Maintain WCAG AA compliance (4.5:1 ratio minimum).
- **Reduced Motion**: Respect `prefers-reduced-motion` setting for animations.
- **Focus Management**: Proper focus handling for modals and dynamic content.

```typescript
// Accessibility patterns
@Component({
  template: `
    <button 
      [attr.aria-label]="buttonLabel"
      [attr.aria-pressed]="isActive"
      (click)="toggle()"
      (keydown.enter)="toggle()">
      {{ buttonText }}
    </button>
  `
})
export class AccessibleButtonComponent {
  @Input() buttonLabel!: string;
  @Input() buttonText!: string;
  @Input() isActive = false;
}
```

#### 🔀 Migration & Versioning

- **Database Migrations**: Always provide upgrade paths for IndexedDB schema changes.
- **Feature Flags**: Use environment variables for gradual feature rollouts.
- **Backward Compatibility**: Maintain compatibility for at least 2 previous versions.
- **Version Management**: Implement proper database versioning with Dexie.js.

```typescript
// Migration pattern
export class PersistenceService extends Dexie {
  constructor() {
    super('RandomSelectorDB');
    
    // Version 1
    this.version(1).stores({
      persons: 'id, name, status'
    });
    
    // Version 2 - add new fields
    this.version(2).stores({
      persons: 'id, name, status, addedAt, imageSize'
    }).upgrade(tx => {
      return tx.table('persons').toCollection().modify(person => {
        person.addedAt = new Date();
        person.imageSize = 0;
      });
    });
  }
}
```

#### 🧪 Testing & Reliability

- **Always create Jasmine/Karma unit tests for new features** (components, services, pipes, etc.).
- **After updating any logic**, check whether existing unit tests need to be updated, and if so, perform the update.
- **Tests live directly next to the file being tested** (e.g., `my-component.component.spec.ts`), as is the Angular standard.
- **E2E tests live in the `/e2e` directory** and are written with **Playwright**.
- **Test Coverage**: Maintain minimum 80% code coverage for services and components.
- **Integration Tests**: Test IndexedDB operations with real database instances.

Every test should cover at least:
- 1 test for the expected use case.
- 1 test for an edge case.
- 1 test for a failure/error case.
- 1 test for accessibility (if UI component).

```typescript
// Enhanced testing patterns
describe('PersonService', () => {
  let service: PersonService;
  let mockPersistence: jasmine.SpyObj<PersistenceService>;

  beforeEach(() => {
    const spy = jasmine.createSpyObj('PersistenceService', ['addPerson', 'deletePerson']);
    TestBed.configureTestingModule({
      providers: [
        { provide: PersistenceService, useValue: spy }
      ]
    });
    service = TestBed.inject(PersonService);
    mockPersistence = TestBed.inject(PersistenceService) as jasmine.SpyObj<PersistenceService>;
  });

  it('should handle storage quota exceeded error', async () => {
    const quotaError = new Error('QuotaExceededError');
    mockPersistence.addPerson.and.returnValue(Promise.reject(quotaError));
    
    const result = await service.addPersonSafely(mockPerson);
    
    expect(result.success).toBeFalse();
    expect(result.error).toContain('storage space');
  });
});
```

#### ✅ Task Management

- **Mark completed tasks in `INITIAL.md` or a `TASKS.md`** immediately after finishing them.
- Add new sub-tasks or TODOs discovered during development to the same document.
- **Use GitHub Issues or similar** for complex feature tracking when available.

#### 📎 Style & Conventions

- The primary language is **TypeScript**.
- **Follow the official Angular Style Guide** and format the code with **Prettier**.
- **Use TypeScript Interfaces/Types** for data validation and modeling with strict typing.
- The framework is **Angular**, and **Angular Material** is used for UI components.
- **Signal-based State Management**: Prefer Angular Signals over traditional observables where appropriate.
- **Standalone Components**: Use standalone components architecture for new components.

Write **TSDoc/JSDoc comments for every public method**:
```typescript
/**
 * Adds a new person to the active pool with image optimization.
 * @param name - The person's display name (max 50 characters)
 * @param imageFile - The uploaded image file (JPEG/PNG/WebP, max 10MB)
 * @returns Promise resolving to the created person or error response
 * @throws {ValidationError} When input validation fails
 * @throws {StorageError} When IndexedDB quota is exceeded
 */
async addPerson(name: string, imageFile: File): Promise<ApiResponse<Person>> {
  // Implementation with proper error handling...
}
```

#### 📚 Documentation & Explainability

- **Update `README.md`** when new features are added, dependencies change, or setup steps are modified.
- **Comment non-obvious code** and ensure everything is understandable to a mid-level developer.
- When writing complex logic, **add an inline comment (`// Reason: ...`)** explaining the "why," not just the "what."
- **Document performance optimizations** and their reasoning.
- **Maintain CHANGELOG.md** for tracking significant changes.

#### 🧠 AI Behavior Rules

- **Never assume missing context. Ask questions if uncertain.**
- **Never hallucinate libraries or functions** – only use known, verified npm packages.
- **Always confirm file paths and module names exist** before referencing them in code or tests.
- **Never delete or overwrite existing code** unless explicitly instructed to or if it's part of a task from `INITIAL.md`.
- **Validate all assumptions** about browser compatibility and feature support.
- **Consider mobile-first responsive design** for all UI components.
- **Always implement proper loading states** for async operations.
- **Handle offline scenarios gracefully** with appropriate user feedback.
