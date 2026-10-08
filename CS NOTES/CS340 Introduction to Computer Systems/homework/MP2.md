# CS340 mp2

### `utf8lib.c`

```c
#include "utf8lib.h"
#include <stdlib.h>
#include <string.h>

struct _replacement_set_t {
  const char ** from;
  const char ** to;
  size_t count;
  size_t size;
};

ReplacementSet *newReplacementSet() {
  ReplacementSet *set = (ReplacementSet *)malloc(sizeof(ReplacementSet));
  
  if (!set) { 
    free(set);

    return NULL;
  }
  
  set->count = 0;
  set->size = 16;

  set->from = (const char**)malloc(set->size * sizeof(const char *));
  set->to = (const char**)malloc(set->size * sizeof(const char *));

  if (!set->from || !set->to) {
    free(set->from);
    free(set->to);
    free(set);

    return NULL;
  }

  return set;
}

void deleteReplacementSet(ReplacementSet *set) {
  if (set) {
    free(set->from);
    free(set->to);
    free(set);
  } 
}

void addReplacement(ReplacementSet *set, const char *from, const char *to) {
  if (!set) { return; }
  if (!from || !to) { return; }

  if (set->count >= set->size) {
    set->size *= 2;

    const char **from_second = (const char**)realloc(set->from, set->size * sizeof(const char *));
    const char **to_second = (const char**)realloc(set->to, set->size * sizeof(const char *));
    
    if (!from_second || !to_second) { return; }

    set->from = from_second;
    set->to = to_second;
  }

  set->from[set->count] = from;
  set->to[set->count] = to;
  
  set->count += 1;
}

char *replaceAll(ReplacementSet *set, const char *input) {
  if (!set || !input) {return NULL;}

  size_t in_leng = strlen(input);

  size_t bf_size = in_leng + 1;
  char *buffer = (char *)malloc(bf_size);
  if (!buffer) {
    return NULL;
  }

  size_t buffer_leng = 0;
  const char *p = input;

  while (*p) {
    size_t m_leng = 0;
    const char *replacement = NULL;
    size_t rep_leng = 0;

    for (size_t i = 0; i < set->count; i++) {
      size_t from_leng = strlen(set->from[i]);

      if (strncmp(p, set->from[i], from_leng) == 0 && from_leng > m_leng) {
        m_leng = from_leng;

        replacement = set->to[i];
        rep_leng = strlen(replacement);
      }
    }

    if (m_leng > 0) {
      while (buffer_leng + rep_leng >= bf_size) {
        bf_size *= 2;

        char *buffer_second = (char *)realloc(buffer, bf_size);

        if (!buffer_second) {
            free(buffer);
            return NULL;
        }

        buffer = buffer_second;
      }

      memcpy(buffer + buffer_leng, replacement, rep_leng);
      buffer_leng += rep_leng;
      p += m_leng;
    } 
    
    else {
      if (buffer_leng + 1 >= bf_size) {
        bf_size *= 2;

        char *buffer_second = (char *)realloc(buffer, bf_size);
        if (!buffer_second) {
          free(buffer);
          return NULL;
        }
        buffer = buffer_second;
      }

      buffer[buffer_leng++] = *p++;
    }
  }
  buffer[buffer_leng] = '\0';

  return buffer;
}


int decodeCharacter(const char **utf8) {
  const char *p = *utf8;
  int codepoint = 0;
  int adv_pt_num  = 0;

  if ((unsigned char)p[0] == 0x00) {
    adv_pt_num = 1;

    codepoint = 0x00;
  }

  else if (((unsigned char)p[0] & 0x80) == 0x00) {
    adv_pt_num = 1;

    codepoint = (unsigned char)p[0];
  }

  else if (((unsigned char)p[0] & 0xE0) == 0xC0) {
    adv_pt_num = 2;

    if (((unsigned char)p[1] & 0xC0) != 0x80) { goto failedDECODE; }

    codepoint = (((unsigned char)p[0] & 0x1F) << 6 ) 
              | ((unsigned char)p[1] & 0x3F); 

    if (codepoint < 0x80) { goto failedDECODE; }
  }

  else if (((unsigned char)p[0] & 0xF0) == 0xE0) {
    adv_pt_num = 3;

    if ( ((unsigned char)p[1] & 0xC0) != 0x80 
        || ((unsigned char)p[2] & 0xC0) != 0x80 ) { goto failedDECODE; }

    codepoint = (((unsigned char)p[0] & 0x0F) << 12) 
              | (((unsigned char)p[1] & 0x3F) << 6) 
              | ((unsigned char)p[2] & 0x3F); 

    if (codepoint < 0x800 
        || (codepoint >= 0xD800 && codepoint <= 0xDFFF)) { goto failedDECODE; }
  }

  else if (((unsigned char)p[0] & 0xF8) == 0xF0) {
    adv_pt_num = 4;

    if ( ((unsigned char)p[1] & 0xC0) != 0x80 
        || ((unsigned char)p[2] & 0xC0) != 0x80 
        || ((unsigned char)p[3] & 0xC0) != 0x80 ) {
      goto failedDECODE;
    }

    codepoint = (((unsigned char)p[0] & 0x07) << 18) 
              | (((unsigned char)p[1] & 0x3F) << 12) 
              | (((unsigned char)p[2] & 0x3F) << 6) 
              | ((unsigned char)p[3] & 0x3F); 

    if (codepoint < 0x10000 || codepoint > 0x10FFFF) { goto failedDECODE; }
  }

  else {
    failedDECODE: return -1;
  }

  *utf8 += adv_pt_num;

  return codepoint;
}

int encodeCharacter(char **utf8, size_t *space, int codepoint) {
  if (!space || !utf8 || !(*utf8)) { goto failedENCODE; }

  int count = 0;
  char *p = *utf8;

  if (codepoint < 0 
      || codepoint > 0x10FFFF) {
     goto failedENCODE;
  }

  else if (codepoint <= 0x7F) {
    if (*space < 1) {goto failedENCODE;}
    
    p[0] = (char)codepoint;
    count = 1;
  }

  else if (codepoint <= 0x7FF) {
    if (*space < 2) { goto failedENCODE; }

    p[0] = 0xC0 | ((codepoint >> 6) & 0x1F);
    p[1] = 0x80 | (codepoint & 0x3F);

    count = 2;    
  }

  else if (codepoint <= 0xFFFF) {
    if (codepoint >= 0xD800 && codepoint <= 0xDFFF) { goto failedENCODE; }
    if (*space < 3) { goto failedENCODE; }

    p[0] = 0xE0 | ((codepoint >> 12) & 0x0F);
    p[1] = 0x80 | ((codepoint >> 6) & 0x3F);
    p[2] = 0x80 | (codepoint & 0x3F);

    count = 3;
  }
  
  else if (codepoint >= 0x10000 && codepoint <= 0x10FFFF) {
    if (*space < 4) { goto failedENCODE; }

    p[0] = 0xF0 | ((codepoint >> 18) & 0x07);
    p[1] = 0x80 | ((codepoint >> 12) & 0x3F);
    p[2] = 0x80 | ((codepoint >> 6) & 0x3F);
    p[3] = 0x80 | (codepoint & 0x3F);

    count = 4;
  }

  else {
    failedENCODE: return 0;
  }
  
    *space -= count;
    *utf8 += count;
    
    return count;
}

size_t strlen8c(const char *s) {
  if (!s) { return 0; }

  size_t ans = 0;
  const char *p = s;

  while (*p) {
    const char *ori_p = p;
    int codepoint = decodeCharacter(&p);
    if (codepoint < 0) { break; }
    ans += 1;
    if (p == ori_p) { break; }
  }

  return ans;
}

size_t strlen8i(const int *s) {
  if (!s) { return 0; }

  size_t ans = 0;
  const int *p = s;

  while ((*p) != 0) {
    int codepoint = *p;

    if (codepoint < 0 || codepoint > 0x10FFFF) { break; }

    if (codepoint <= 0x7F) {
      ans += 1;
    }
    else if (codepoint <= 0x7FF) {
      ans += 2;
    }
    else if (codepoint <= 0xFFFF) {
      if (codepoint >= 0xD800 && codepoint <= 0xDFFF) { break; }
      ans += 3;
    }
    else {
      ans += 4;
    }
    p++;
  }

  return ans;
}
```
