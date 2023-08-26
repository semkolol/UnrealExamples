---
title: Spawn Actor
slug: SpawnActor
docs: https://docs.unrealengine.com/5.2/en-US/spawning-actors-in-unreal-engine/
---

<br />

_Description from [Documentation](https://docs.unrealengine.com/5.2/en-US/spawning-actors-in-unreal-engine/):_
The process of creating a new instance of an Actor is known as spawning. Spawning of Actors is performed using the UWorld::SpawnActor() function. This function creates a new instance of a specified class and returns a pointer to the newly created Actor. UWorld::SpawnActor() may only be used for creating instances of classes which inherit from the Actor class in their hierarchy.

<br />


**SomeClass.h**

```cpp
#include "TheClassYouWantToSpawn.h"
class AYourClass : public AActor
{
...
  // What Class to Spawn
	UPROPERTY(EditDefaultsOnly, BlueprintReadOnly, Category = "YourGame|Actor")
	TSubclassOf<ATheClassYouWantToSpawn> TheClassYouWantToSpawn;

  // Reference to the Spawned instance
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "YourGame|Actor", meta = (AllowPrivateAccess = "true"))
	ATheClassYouWantToSpawn* SpawnedClass;
...
}
```

<br />

**SomeClass.cpp**

```cpp
// for this example we'll just spawn it on BeginPlay
void AYourClass::BeginPlay()
{
	// Call the base class  
	Super::BeginPlay();

  ...

	// Spawn Something in the world at Location 0,0,0
  if (TheClassYouWantToSpawn)
  {
    SpawnedClass = GetWorld()->SpawnActor<ATheClassYouWantToSpawn>(TheClassYouWantToSpawn, FVector::ZeroVector, FRotator::ZeroRotator);
  }

}
```
<br />
