## FEATURE:

**Random Selection System (Offline-First Web App)**

This feature is a comprehensive, client-side web application built with Angular 19+ for managing and randomly selecting participants. The application is designed to be "offline-first," persisting all data, including user-uploaded images, locally in the browser using **`IndexedDB`**.

The user interface is a responsive, three-column layout as depicted in the provided screenshot:

1.  **Person Management (Left Column)**:
    * **Add Person**: A form to add a new participant by entering a name and uploading an image file from their device.
    * **Available People List**: A scrollable list of all participants, where each entry displays the person's avatar and name. Each person has a clear visual status indicator ("In slot machine" or "Available") and corresponding action buttons:
        * **Add**: Moves an "available" person into the active pool for the slot machine.
        * **Remove**: Takes an "active" person out of the slot machine, making them "available" again.
        * **Delete (Trash Icon)**: Permanently removes the person and their image from the application.

2.  **Slot Machine (Center Column)**:
    * The primary visual and interactive element, displaying reels with the images and names of the "active" participants.
    * A main **"Select Random"** button to initiate the animated selection process.
    * Two secondary control buttons:
        * A **"Reset/Re-spin"** icon button.
        * An **"Expand/Fullscreen"** icon button.

3.  **Selection History (Right Column)**:
    * A log of previous winning selections.
    * Each entry clearly displays the winner's avatar, name, the precise **timestamp** of the win, and a decorative trophy icon.

A header provides at-a-glance application stats, including the number of `active` and `total` participants, and a visual indicator for `Offline Mode`.

---

## PERFORMANCE REQUIREMENTS:

- **Image Optimization**: 
  - Automatically resize uploaded images to max 512x512px
  - Compress images to <100KB before IndexedDB storage
  - Show compression progress for large files
  - Maintain aspect ratio during resizing

- **List Performance**: 
  - Handle 1000+ participants efficiently with virtual scrolling (Angular CDK)
  - Implement trackBy functions for all *ngFor loops
  - Use OnPush change detection strategy for list items

- **Database Performance**:
  - Handle IndexedDB quota limits (typically 50MB per origin)
  - Batch database operations for bulk actions
  - Implement database transaction rollback for failed operations

- **Animation Performance**: 
  - Slot machine animation must run at consistent 60fps
  - Use CSS transforms for smooth animations
  - Respect `prefers-reduced-motion` setting
  - Provide fallback static selection for low-performance devices

- **Bundle Optimization**:
  - Lazy load feature modules
  - Tree-shake unused dependencies
  - Compress images and assets
  - Total initial bundle size <1MB

---

## ERROR STATES & EDGE CASES:

- **Storage Management**:
  - **Storage Full**: Graceful handling when IndexedDB quota exceeded with clear user messaging
  - **Corrupted Data**: Recovery mechanisms for corrupted person records with data validation
  - **Large Images**: User feedback for oversized uploads (>10MB) with compression options
  - **Browser Compatibility**: Fallback messaging for browsers without IndexedDB support

- **Network & Connectivity**:
  - **Offline Mode**: Full functionality without internet connection
  - **File Loading Errors**: Retry mechanisms for corrupted image files
  - **Browser Crashes**: Auto-recovery of unsaved state on app restart

- **User Input Validation**:
  - **Empty Names**: Prevent adding participants without names
  - **Duplicate Names**: Handle or prevent duplicate participant names
  - **Invalid Files**: Clear messaging for non-image file uploads
  - **No Participants**: Handle slot machine with zero active participants

- **Animation Edge Cases**:
  - **Single Participant**: Proper animation behavior with only one active person
  - **Mid-Animation Interruption**: Handle user interactions during animation
  - **Window Resize**: Responsive animation during viewport changes

---

## ACCESSIBILITY REQUIREMENTS:

- **Screen Reader Support**: 
  - All content must be accessible via screen reader with proper ARIA labels
  - Descriptive alt text for all participant images
  - Live regions for dynamic content updates (new selections, status changes)

- **Keyboard Navigation**: 
  - Full functionality accessible via keyboard only
  - Logical tab order throughout the application
  - Escape key closes modal dialogs
  - Enter/Space activates buttons and triggers actions

- **Visual Accessibility**:
  - High contrast mode support with minimum 4.5:1 color ratio
  - Scalable text up to 200% without layout breaking
  - Clear focus indicators for all interactive elements
  - Motion preferences respect (`prefers-reduced-motion`)

- **Cognitive Accessibility**:
  - Clear, simple language in all UI text
  - Consistent navigation patterns
  - Undo functionality for critical actions (delete participant)
  - Progress indicators for long-running operations

---

## SECURITY CONSIDERATIONS:

- **File Upload Security**:
  - **MIME Type Validation**: Only allow image MIME types (`image/jpeg`, `image/png`, `image/webp`)
  - **File Header Validation**: Verify actual file content matches declared MIME type
  - **Size Limits**: Maximum 10MB per image, maximum 100 participants total
  - **Content Scanning**: Basic validation to ensure uploaded files are actual images

- **Data Protection**:
  - **Local Storage Only**: All data remains in browser's IndexedDB
  - **No Network Transmission**: No participant data sent to external servers
  - **Data Encryption**: Consider encrypting sensitive data in IndexedDB
  - **Secure Cleanup**: Proper cleanup of temporary Object URLs to prevent memory leaks

- **XSS Prevention**:
  - **Input Sanitization**: Sanitize all user-provided names and text
  - **CSP Headers**: Implement Content Security Policy for XSS prevention
  - **Safe DOM Manipulation**: Use Angular's DomSanitizer for dynamic content

- **Browser Security**:
  - **Same-Origin Policy**: Ensure compliance with browser security policies
  - **Feature Detection**: Graceful degradation for unsupported browser features
  - **Secure Contexts**: Ensure HTTPS requirement for sensitive operations

---

## TECHNICAL ARCHITECTURE:

```typescript
// Core data models with strict typing
export type PersonStatus = 'available' | 'active';

export interface Person {
  id: string;
  name: string;
  status: PersonStatus;
  image: File | Blob;
  addedAt: Date;
  imageSize: number;
  lastSelected?: Date;
}

export interface SelectionHistory {
  id: string;
  personId: string;
  personName: string;
  selectedAt: Date;
  sessionId: string;
}

export interface AppConfig {
  maxParticipants: number;
  maxImageSize: number; // in bytes
  supportedImageTypes: string[];
  enableOfflineMode: boolean;
  animationDuration: number;
  compressionQuality: number;
}

export interface ErrorState {
  code: 'STORAGE_FULL' | 'INVALID_FILE' | 'NETWORK_ERROR' | 'QUOTA_EXCEEDED' | 'VALIDATION_ERROR';
  message: string;
  recovery?: string;
  timestamp: Date;
}

// Performance monitoring
export interface PerformanceMetrics {
  imageLoadTime: number;
  databaseQueryTime: number;
  animationFrameRate: number;
  memoryUsage: number;
}

// API Response pattern
export interface ApiResponse<T> {
  data?: T;
  error?: ErrorState;
  success: boolean;
  timestamp: Date;
  metadata?: {
    processingTime: number;
    cacheHit: boolean;
  };
}
```

---

## EXAMPLES:

The following code snippets illustrate the core architectural patterns required for this enhanced feature set.

### Example 1: Enhanced `persistence.service.ts` using Dexie.js

```typescript
import { Injectable } from '@angular/core';
import Dexie, { Table } from 'dexie';
import { Person, SelectionHistory, AppConfig } from '../models/person.model';

@Injectable({ providedIn: 'root' })
export class PersistenceService extends Dexie {
  persons!: Table<Person, string>;
  history!: Table<SelectionHistory, string>;
  config!: Table<AppConfig, string>;

  constructor() {
    super('RandomSelectorDB');
    
    // Version 1
    this.version(1).stores({
      persons: 'id, name, status, addedAt',
      history: 'id, personId, selectedAt',
      config: 'id'
    });

    // Version 2 - Add performance fields
    this.version(2).stores({
      persons: 'id, name, status, addedAt, imageSize, lastSelected',
      history: 'id, personId, selectedAt, sessionId',
      config: 'id, version'
    }).upgrade(tx => {
      return tx.table('persons').toCollection().modify(person => {
        person.imageSize = person.image?.size || 0;
        person.lastSelected = null;
      });
    });
  }

  async addPersonWithValidation(name: string, imageFile: File): Promise<ApiResponse<Person>> {
    const startTime = performance.now();
    
    try {
      // Validate input
      const validation = this.validatePersonInput(name, imageFile);
      if (!validation.success) {
        return validation;
      }

      // Optimize image
      const optimizedImage = await this.optimizeImage(imageFile);
      
      const newPerson: Person = {
        id: crypto.randomUUID(),
        name: name.trim(),
        status: 'available',
        image: optimizedImage,
        addedAt: new Date(),
        imageSize: optimizedImage.size
      };

      await this.persons.add(newPerson);
      
      return {
        data: newPerson,
        success: true,
        timestamp: new Date(),
        metadata: {
          processingTime: performance.now() - startTime,
          cacheHit: false
        }
      };
    } catch (error) {
      console.error('Failed to add person:', error);
      return {
        error: {
          code: 'STORAGE_FULL',
          message: 'Unable to save participant. Storage may be full.',
          recovery: 'Try removing some participants or clearing browser data.',
          timestamp: new Date()
        },
        success: false,
        timestamp: new Date()
      };
    }
  }

  private validatePersonInput(name: string, imageFile: File): ApiResponse<null> {
    if (!name?.trim()) {
      return {
        success: false,
        timestamp: new Date(),
        error: {
          code: 'VALIDATION_ERROR',
          message: 'Name is required',
          timestamp: new Date()
        }
      };
    }

    const allowedTypes = ['image/jpeg', 'image/png', 'image/webp'];
    if (!allowedTypes.includes(imageFile.type)) {
      return {
        success: false,
        timestamp: new Date(),
        error: {
          code: 'INVALID_FILE',
          message: 'Only JPEG, PNG, and WebP images are allowed',
          timestamp: new Date()
        }
      };
    }

    const maxSize = 10 * 1024 * 1024; // 10MB
    if (imageFile.size > maxSize) {
      return {
        success: false,
        timestamp: new Date(),
        error: {
          code: 'INVALID_FILE',
          message: 'Image size must be less than 10MB',
          recovery: 'Please compress the image or choose a smaller file',
          timestamp: new Date()
        }
      };
    }

    return { success: true, timestamp: new Date() };
  }

  private async optimizeImage(file: File): Promise<Blob> {
    return new Promise((resolve) => {
      const canvas = document.createElement('canvas');
      const ctx = canvas.getContext('2d')!;
      const img = new Image();

      img.onload = () => {
        // Calculate new dimensions (max 512x512 while maintaining aspect ratio)
        const maxSize = 512;
        let { width, height } = img;
        
        if (width > height) {
          if (width > maxSize) {
            height = (height * maxSize) / width;
            width = maxSize;
          }
        } else {
          if (height > maxSize) {
            width = (width * maxSize) / height;
            height = maxSize;
          }
        }

        canvas.width = width;
        canvas.height = height;
        
        ctx.drawImage(img, 0, 0, width, height);
        
        canvas.toBlob(resolve!, 'image/jpeg', 0.8);
      };

      img.src = URL.createObjectURL(file);
    });
  }
}
```

### Example 2: Enhanced `person-card.component.ts` with Accessibility

```typescript
import { Component, Input, OnInit, OnDestroy, inject, signal } from '@angular/core';
import { DomSanitizer, SafeUrl } from '@angular/platform-browser';
import { Person } from '../models/person.model';
import { StateService } from '../core/services/state.service';

@Component({
  selector: 'app-person-card',
  template: `
    <div class="person-card" 
         [attr.aria-label]="accessibilityLabel()"
         [class.active]="person.status === 'active'">
      
      <img [src]="imageUrl()" 
           [alt]="'Photo of ' + person.name"
           class="person-avatar"
           (error)="onImageError()"
           loading="lazy">
      
      <div class="person-info">
        <h3 class="person-name" [id]="'name-' + person.id">{{ person.name }}</h3>
        <span class="person-status" 
              [attr.aria-describedby]="'name-' + person.id"
              [class]="'status-' + person.status">
          {{ person.status === 'active' ? 'In slot machine' : 'Available' }}
        </span>
      </div>
      
      <div class="person-actions" role="group" [attr.aria-label]="'Actions for ' + person.name">
        <button type="button"
                class="btn-toggle"
                [attr.aria-label]="toggleButtonLabel()"
                [attr.aria-pressed]="person.status === 'active'"
                (click)="toggleActiveState()"
                (keydown.enter)="toggleActiveState()">
          {{ person.status === 'available' ? 'Add' : 'Remove' }}
        </button>
        
        <button type="button"
                class="btn-delete"
                [attr.aria-label]="'Delete ' + person.name"
                (click)="confirmDelete()"
                (keydown.enter)="confirmDelete()">
          <span aria-hidden="true">🗑️</span>
          <span class="sr-only">Delete</span>
        </button>
      </div>
    </div>
  `,
  styleUrls: ['./person-card.component.scss']
})
export class PersonCardComponent implements OnInit, OnDestroy {
  @Input({ required: true }) person!: Person;

  private readonly sanitizer = inject(DomSanitizer);
  private readonly stateService = inject(StateService);
  
  protected readonly imageUrl = signal<SafeUrl | null>(null);
  protected readonly hasImageError = signal(false);
  private objectUrl: string | null = null;

  ngOnInit(): void {
    this.createImageUrl();
  }

  ngOnDestroy(): void {
    this.revokeImageUrl();
  }

  protected accessibilityLabel(): string {
    const status = this.person.status === 'active' ? 'active in slot machine' : 'available';
    return `${this.person.name}, ${status}`;
  }

  protected toggleButtonLabel(): string {
    const action = this.person.status === 'available' ? 'Add to' : 'Remove from';
    return `${action} slot machine`;
  }

  private createImageUrl(): void {
    if (this.person.image) {
      this.objectUrl = URL.createObjectURL(this.person.image);
      this.imageUrl.set(this.sanitizer.bypassSecurityTrustUrl(this.objectUrl));
    }
  }

  private revokeImageUrl(): void {
    if (this.objectUrl) {
      URL.revokeObjectURL(this.objectUrl);
      this.objectUrl = null;
    }
  }

  protected onImageError(): void {
    this.hasImageError.set(true);
    console.warn(`Failed to load image for person: ${this.person.name}`);
  }

  protected toggleActiveState(): void {
    const newStatus = this.person.status === 'available' ? 'active' : 'available';
    this.stateService.updatePersonStatus(this.person.id, newStatus);
  }

  protected confirmDelete(): void {
    if (confirm(`Are you sure you want to delete ${this.person.name}? This action cannot be undone.`)) {
      this.stateService.deletePerson(this.person.id);
    }
  }
}
```

### Example 3: Performance Monitoring Service

```typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';
import { PerformanceMetrics } from '../models/person.model';

@Injectable({ providedIn: 'root' })
export class PerformanceService {
  private metricsSubject = new BehaviorSubject<PerformanceMetrics>({
    imageLoadTime: 0,
    databaseQueryTime: 0,
    animationFrameRate: 60,
    memoryUsage: 0
  });

  metrics$: Observable<PerformanceMetrics> = this.metricsSubject.asObservable();

  measureDatabaseOperation<T>(operation: () => Promise<T>): Promise<T> {
    const startTime = performance.now();
    
    return operation().finally(() => {
      const endTime = performance.now();
      const currentMetrics = this.metricsSubject.value;
      
      this.metricsSubject.next({
        ...currentMetrics,
        databaseQueryTime: endTime - startTime
      });
    });
  }

  startFrameRateMonitoring(): void {
    let frames = 0;
    let lastTime = performance.now();
    
    const measureFrameRate = (currentTime: number) => {
      frames++;
      
      if (currentTime - lastTime >= 1000) {
        const currentMetrics = this.metricsSubject.value;
        this.metricsSubject.next({
          ...currentMetrics,
          animationFrameRate: frames
        });
        
        frames = 0;
        lastTime = currentTime;
      }
      
      requestAnimationFrame(measureFrameRate);
    };
    
    requestAnimationFrame(measureFrameRate);
  }
}
```

---

## DOCUMENTATION:

* **Angular Standalone Components**: `https://angular.dev/guide/standalone-components`
* **Angular Signals**: `https://angular.dev/guide/signals`
* **Angular Animations**: `https://angular.dev/guide/animations`
* **Angular CDK Virtual Scrolling**: `https://material.angular.io/cdk/scrolling/overview`
* **IndexedDB API**: `https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API`
    * **Reason**: Essential for understanding the underlying client-side database technology for storing files.
* **Dexie.js**: `https://dexie.org/`
    * **Reason**: Wrapper for `IndexedDB` that simplifies database operations and provides transaction management.
* **Web Content Accessibility Guidelines (WCAG)**: `https://www.w3.org/WAI/WCAG21/quickref/`
    * **Reason**: Compliance requirements for accessibility features.
* **Canvas API for Image Processing**: `https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API`
    * **Reason**: Required for client-side image optimization and resizing.

---

## OTHER CONSIDERATIONS:

* **The Persistence Layer is Critical**: A well-structured `PersistenceService` with comprehensive error handling and data validation is the foundation. All operations must be atomic and include proper rollback mechanisms.

* **Complex State Management**: The application requires sophisticated state management for person status, selection history, and performance metrics. Consider using Angular's SignalStore for centralized state management.

* **Client-Side Image Processing**: Implement robust image optimization pipeline with progress feedback. Consider using Web Workers for heavy image processing to avoid blocking the main thread.

* **Memory Management**: Critical for long-running sessions. Implement proper cleanup of Object URLs, component subscriptions, and large data structures. Monitor memory usage and provide warnings when approaching limits.

* **Progressive Enhancement**: Ensure basic functionality works without JavaScript, then enhance with advanced features. Provide fallbacks for older browsers or limited capabilities.

* **Internationalization (i18n)**: Design the application to support multiple languages from the start, even if initially only implementing English.

* **Testing Strategy**: Implement comprehensive testing including unit tests for all services, integration tests for IndexedDB operations, accessibility testing with axe-core, and E2E testing with Playwright.

* **Deployment Considerations**: As a client-side application, consider implementing a Service Worker for better offline capabilities and caching strategies for static assets.