#### Introduction
Simple parser of SMBIOS structure tables. Nothing more, nothing less.<br />
Code is purely for studying purposes: minimum oop, readability in cost of error handling and assuming that you always feed right data.<br />
Read <a href="http://dmtf.org/sites/default/files/standards/documents/DSP0134_2.8.0.pdf">SMBIOS documentation</a> for details.

#### Installation
Just include header file where needed.

#### Usage
``` c++
#include <cstdio>
#include "smbios.hpp"

int main() {
  using namespace smbios;
  
  parser meta;
  byte_t *buff = nullptr;
  size_t  buff_size = 0;
  
  //////////////////////////////////////////////////////
  // Code that makes buffer to point (or hold) smbios //
  // structure table. Don't forget about size.        //
  //////////////////////////////////////////////////////

  meta.feed(buff, buff_size);
  
  for (auto it = meta.headers.begin(); it != meta.headers.end(); ++it) {
  	string_array_t strings;
  
  	parser::extract_strings(*it, strings);
  
  	switch ((*it)->type) {
  	// ...
  		case types::memory_device: {
  			auto x = static_cast<mem_device *>(*it);
  
  			if (x->total_width == 0)
  				break;
  
  			printf("Memory device %s (%s):\n", strings[x->device_locator], strings[x->bank_locator]);
  			printf("  Data width:    %d bit\n", x->data_width);
  			printf("  Size:          %d M\n",   x->size);
  			printf("  Speed:         %d MHz\n", x->speed);
  			printf("  Clock speed:   %d MHz\n", x->clock_speed);
  			printf("  Manufacturer:  %s\n",     strings[x->manufacturer]);
  			printf("  S/N:           %s\n",     strings[x->serial_number]);
  			printf("  Tag number:    %s\n",     strings[x->assert_tag]);
  			printf("  Part number:   %s\n",     strings[x->part_number]);
  			printf("\n");
  		} break;
  
  	// ...
  	// Handle all other struct types
  	// ...
  
  		default: printf("Unknown structure type %d\n", (*it)->type);
  	}
  }
  
  //////////////////
  // Free buffers //
  //////////////////

  return 0;
}
```
