# CS340 mp4

### `allocator.c`

```c
#include "allocator.h"
#include <string.h>
#include <stdio.h>

static void *base;
static size_t used;

typedef struct meta_deta {
  size_t size;
  int is_free;
  struct meta_deta *next;
  struct meta_deta *prev;
} Metadata;

Metadata *free_list;

void allocator_init(void *newbase) {
  base = newbase;
  used = 0;
  free_list = NULL;
}

void allocator_reset() { 
  used = 0;
  free_list = NULL;
}

void *mymalloc(size_t size) {
  if (size == 0) {
    return NULL;
  }

  Metadata *current = free_list;
  while (current != NULL) {
    if (current->size >= size) {
      if (current->prev) {
        current->prev->next = current->next;
      } 
      else {
        free_list = current->next;
      }

      if (current->next) {
        current->next->prev = current->prev;
      }

      current->is_free = 0;

      if (current->size >= size + sizeof(Metadata) + 1) {
        Metadata *new_meta = (Metadata*)((char*)(current + 1) + size);
        new_meta->size = current->size - size - sizeof(Metadata);
        new_meta->is_free = 1;
        new_meta->next = free_list;
        new_meta->prev = NULL;
        if (free_list != NULL) {
          free_list->prev = new_meta;
        }
        free_list = new_meta;
        current->size = size;
      }

      return (void *)(current + 1);
    }
    current = current->next;
  }

  size_t total_size = size + sizeof(Metadata);
  void *ans = (char *)base + used;
  Metadata *meta = (Metadata *)ans;
  meta->size = size;
  meta->is_free = 0;
  meta->next = NULL;
  meta->prev = NULL;
  used += total_size;
  return (void *)(meta + 1);
}

void myfree(void *ptr) {
  if (ptr == NULL) { return; }
  Metadata *meta = ((Metadata *)ptr) - 1;

  meta->is_free = 1;

  meta->next = free_list;
  if (free_list != NULL) {
    free_list->prev = meta;
  }
  meta->prev = NULL;
  free_list = meta;

  Metadata *next_meta = (Metadata*)((char*)(meta + 1) + meta->size);
  if ((char*)next_meta < (char*)base + used && next_meta->is_free) {
    meta->size += sizeof(Metadata) + next_meta->size;
    if (next_meta->prev) {
      next_meta->prev->next = next_meta->next;
    } 
    else {
      free_list = next_meta->next;
    }
    if (next_meta->next) {
      next_meta->next->prev = next_meta->prev;
    }
  }

  if ((char *)(meta + 1) + meta->size == (char *)base + used) {
    used -= (meta->size + sizeof(Metadata));
    if (meta->prev) {
      meta->prev->next = meta->next;
    } 
    else {
      free_list = meta->next;
    }

    if (meta->next) {
      meta->next->prev = meta->prev;
    }
  }
}

void *myrealloc(void *ptr, size_t size) {
  if (ptr == NULL) { 
    return mymalloc(size); 
  }

  if (size == 0) { 
    myfree(ptr); 
    return NULL;
  }

  Metadata *meta = ((Metadata *)ptr) - 1;

  if ((char *)meta < (char *)base || (char *)meta >= (char *)base + used) {
    return NULL;
  }

  if (meta->size >= size) {
    if (meta->size >= size + sizeof(Metadata) + 1) {
      size_t temp_size_diff = meta->size - size; 

      if ((char *)(meta + 1) + meta->size == (char *)base + used) {
        used -= temp_size_diff;
        meta->size = size;
      } 
      else {
        Metadata *new_meta = (Metadata*)((char*)(meta + 1) + size);
        new_meta->size = meta->size - size - sizeof(Metadata);
        new_meta->is_free = 1;
        new_meta->next = free_list;
        new_meta->prev = NULL;
        if (free_list != NULL) {
          free_list->prev = new_meta;
        }
        free_list = new_meta;
      }
    }

    return ptr;
  } 
  else if ((char *)(meta + 1) + meta->size == (char *)base + used) {
    size_t temp_size_diff = size - meta->size;
    meta->size += temp_size_diff;
    used += temp_size_diff;
    return ptr;
  } 
  else {
    void *new_ptr = mymalloc(size);
    if (new_ptr == NULL) {
      return NULL;
    }

    size_t copy_size = meta->size < size ? meta->size : size;
    memcpy(new_ptr, ptr, copy_size);

    myfree(ptr);

    return new_ptr;
  }
}
```

### `mytest.c`

```c
#include "testharness.h"

const char *mytest(allocator *a) {
  // TO DO: allocate and deallocate using a->malloc, a->realloc, and a->free
  // optionally, do actual work and verify the results
  return 0; // 0 (NULL) on success, or "error message" (a string) on failure.
}
```
