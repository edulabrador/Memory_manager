#ifndef __PROGTEST__
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <cstdint>
#include <cassert>
#include <cmath>
using namespace std;
#endif /* __PROGTEST__ */

struct Block
{
  int size;
  bool isFree;
  Block *next;
};

const int blockSize = sizeof(Block);
Block *freeListStart = nullptr;

void HeapInit(void *memPool, int memSize)
{
  uintptr_t address = reinterpret_cast<uintptr_t>(memPool);
  size_t alignment = alignof(double);
  size_t offset = (alignment - (address % alignment)) % alignment;

  freeListStart = reinterpret_cast<Block *>(address + offset);
  freeListStart->size = memSize - blockSize - offset;
  freeListStart->isFree = true;
  freeListStart->next = nullptr;
}

void *HeapAlloc(int size)
{
  if (size <= 0)
    return nullptr;

  Block *current = freeListStart;

  while (current != nullptr)
  {
    if (current->isFree && current->size >= size)
    {
      break;
    }
    current = current->next;
  }

  if (current != nullptr)
  {
    while (current->size >= (size + blockSize) * 2)
    {
      int newBlockSize = (current->size / 2) - blockSize;
      Block *newBlock = reinterpret_cast<Block *>(reinterpret_cast<char *>(current) + blockSize + newBlockSize);
      newBlock->size = newBlockSize;
      newBlock->isFree = true;
      newBlock->next = current->next;
      current->next = newBlock;
      current->size = newBlockSize;
    }
    current->isFree = false;
    return reinterpret_cast<char *>(current) + blockSize;
  }
  return nullptr;
}

bool HeapFree(void *blk)
{
  Block *blockToFree = reinterpret_cast<Block *>(reinterpret_cast<char *>(blk) - blockSize);
  Block *currentBlock = freeListStart;

  // check if blockToFree is in the list
  while (currentBlock != nullptr)
  {
    if (currentBlock == blockToFree)
    {
      break;
    }
    currentBlock = currentBlock->next;
  }

  if ((!blockToFree->isFree) && currentBlock == blockToFree && currentBlock != nullptr)
  {
    blockToFree->isFree = true;

    while (blockToFree->next != nullptr && blockToFree->next->isFree)
    {
      blockToFree->size += blockToFree->next->size + blockSize;
      blockToFree->next = blockToFree->next->next;
    }
    return true;
  }
  return false;
}

void HeapDone(int *pendingBlk)
{
  *pendingBlk = 0;
  Block *current = freeListStart;
  while (current != nullptr)
  {
    if (!current->isFree)
    {
      (*pendingBlk)++;
    }
    current = current->next;
  }
}

#ifndef __PROGTEST__
int main(void)
{
    // TESTS
  uint8_t *p0, *p1, *p2, *p3, *p4;
  int pendingBlk;
  static uint8_t memPool[3 * 1048576];

  HeapInit(memPool, 2097152);
  assert((p0 = (uint8_t *)HeapAlloc(512000)) != NULL);
  memset(p0, 0, 512000);
  assert((p1 = (uint8_t *)HeapAlloc(511000)) != NULL);
  memset(p1, 0, 511000);
  assert((p2 = (uint8_t *)HeapAlloc(26000)) != NULL);
  memset(p2, 0, 26000);
  HeapDone(&pendingBlk);
  assert(pendingBlk == 3);

  HeapInit(memPool, 2097152);
  assert((p0 = (uint8_t *)HeapAlloc(1000000)) != NULL);
  memset(p0, 0, 1000000);
  assert((p1 = (uint8_t *)HeapAlloc(250000)) != NULL);
  memset(p1, 0, 250000);
  assert((p2 = (uint8_t *)HeapAlloc(250000)) != NULL);
  memset(p2, 0, 250000);
  assert((p3 = (uint8_t *)HeapAlloc(250000)) != NULL);
  memset(p3, 0, 250000);
  assert((p4 = (uint8_t *)HeapAlloc(50000)) != NULL);
  memset(p4, 0, 50000);
  assert(HeapFree(p2));
  assert(HeapFree(p4));
  assert(HeapFree(p3));
  assert(HeapFree(p1));
  assert((p1 = (uint8_t *)HeapAlloc(500000)) != NULL);
  memset(p1, 0, 500000);
  assert(HeapFree(p0));
  assert(HeapFree(p1));
  HeapDone(&pendingBlk);
  assert(pendingBlk == 0);

  HeapInit(memPool, 2359296);
  assert((p0 = (uint8_t *)HeapAlloc(1000000)) != NULL);
  memset(p0, 0, 1000000);
  assert((p1 = (uint8_t *)HeapAlloc(500000)) != NULL);
  memset(p1, 0, 500000);
  assert((p2 = (uint8_t *)HeapAlloc(500000)) != NULL);
  memset(p2, 0, 500000);
  assert((p3 = (uint8_t *)HeapAlloc(500000)) == NULL);
  assert(HeapFree(p2));
  assert((p2 = (uint8_t *)HeapAlloc(300000)) != NULL);
  memset(p2, 0, 300000);
  assert(HeapFree(p0));
  assert(HeapFree(p1));
  HeapDone(&pendingBlk);
  assert(pendingBlk == 1);

  HeapInit(memPool, 2359296);
  assert((p0 = (uint8_t *)HeapAlloc(1000000)) != NULL);
  memset(p0, 0, 1000000);
  assert(!HeapFree(p0 + 1000));
  HeapDone(&pendingBlk);
  assert(pendingBlk == 1);

  // My own test to check out if my code works

  // Test for minimum allocation size
  HeapInit(memPool, 2359296);
  assert((p0 = (uint8_t *)HeapAlloc(1)) != NULL);
  memset(p0, 0, 1);
  assert(HeapFree(p0));
  HeapDone(&pendingBlk);
  assert(pendingBlk == 0);

  // Test for allocation just below and above the total size
  HeapInit(memPool, 2359296);
  assert((p0 = (uint8_t *)HeapAlloc(2359296 - sizeof(Block))) != NULL);
  memset(p0, 0, 2359296 - sizeof(Block));
  assert(HeapFree(p0));
  assert((p0 = (uint8_t *)HeapAlloc(2359296)) == NULL);
  HeapDone(&pendingBlk);
  assert(pendingBlk == 0);

  // Test for multiple small allocations
  HeapInit(memPool, 2359296);
  for (int i = 0; i < 100000; i++)
  {
    assert((p0 = (uint8_t *)HeapAlloc(1)) != NULL);
    memset(p0, 0, 1);
    assert(HeapFree(p0));
  }
  HeapDone(&pendingBlk);
  assert(pendingBlk == 0);
  /*
  // Test for freeing null pointer:
  HeapInit(memPool, 2359296);
  assert(!HeapFree(NULL));
  HeapDone(&pendingBlk);
  assert(pendingBlk == 0);
  */
  // Test for freeing memory not allocated by HeapAlloc
  HeapInit(memPool, 2359296);
  assert(!HeapFree(memPool));
  HeapDone(&pendingBlk);
  assert(pendingBlk == 0);

  // Add more tests here to cover different cases
  HeapInit(memPool, 2359296);
  assert((p0 = (uint8_t *)HeapAlloc(1000000)) != NULL);
  memset(p0, 0, 1000000);
  assert((p1 = (uint8_t *)HeapAlloc(1000000)) != NULL);
  assert(HeapFree(p0));
  HeapDone(&pendingBlk);
  assert(pendingBlk == 1);

  HeapInit(memPool, 2359296);
  assert((p0 = (uint8_t *)HeapAlloc(1000000)) != NULL);
  memset(p0, 0, 1000000);
  assert((p1 = (uint8_t *)HeapAlloc(1000000)) != NULL);
  assert(HeapFree(p1));
  assert(HeapFree(p0));
  HeapDone(&pendingBlk);
  assert(pendingBlk == 0);
  return 0;
}
#endif /* PROGTEST */
