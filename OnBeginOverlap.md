---
title: OnComponentBeginOverlap
slug: OnComponentBeginOverlap
docs: https://docs.unrealengine.com/5.2/en-US/API/Runtime/Engine/Components/UPrimitiveComponent/OnComponentBeginOverlap/
---

<br />

_Description from [Documentation](https://docs.unrealengine.com/5.2/en-US/API/Runtime/Engine/Components/UPrimitiveComponent/OnComponentBeginOverlap/):_

Event called when something starts to overlaps this component, for example a player walking into a trigger. 

For events when objects have a blocking collision, for example a player hitting a wall, see 'Hit' events.

<br />

***

<br />

@note Both this component and the other one must have **GetGenerateOverlapEvents()** set to true to generate overlap events. 

@note When receiving an overlap from another object's movement, the directions of 'Hit.Normal' and 'Hit.ImpactNormal' will be adjusted to indicate force from the other object against this object.

<br />

<br />

**.h**

```cpp
#include "Components/SphereComponent.h" // add this to your includes
//#include "Components/BoxComponent.h" // or another Collision Volume

protected:
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Collision")
		UShapeComponent* CollisionVolume; // Give it a fitting Name

public:
  // Call this Function however you want
  UFUNCTION()
	void OnXXXOverlap(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor,
		UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep,
		const FHitResult& SweepResult);
```

**.cpp**

```cpp
// Input
AActor::AActor()
{
	// Add this to your Constructor
  // Create the Collision Volume and Attach it to a component or to the RootComponent
  CollisionVolume = CreateDefaultSubobject<USphereComponent>(TEXT("CollisionVolume"));
  CollisionVolume->SetupAttachment(RootComponent); // e.g.: Attach to SkeletalMeshComponent
  CollisionVolume->OnComponentBeginOverlap.AddDynamic(this, &AActor::OnXXXOverlap); // The Function we created before
}

// 
void AActor::OnXXXOverlap(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor,
    UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep,
    const FHitResult& SweepResult)
{
  // Check if OtherActor is not null && check for a specific Class - this is optional
  if (OtherActor && OtherActor->IsA(AAnotherActor::StaticClass()) )
  {
    // do something here...

    // here is an example:
    AAnotherActor* AnotherActor = Cast<AAnotherActor>(OtherActor); // Cast to the Actor that got got

    if (AnotherActor)
    {
      // do something with the actor e.g. apply damage
      AnotherActor->Health -= 10.f;
    }
  }
}
```
<br />

<br />

Also: dont forget to enable Overlap in the Actors Properties under Collision and set it to OverlapAllDynamics.