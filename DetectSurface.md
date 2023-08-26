---
title: Detect Surface (e.g. for climbing)
slug: DetectSurface
docs: https://docs.unrealengine.com/5.2/en-US/API/
---

<br />

Detect the Surface: get important data for example the angle to decide if it's climable or not.


<br />



First we need something to detect the Mesh/Object that got hit.

So we'll use a LineTrace for that:

**YourCharacter.cpp**

```cpp
AActor::AActor()
{
  // Optional: Define something, that you want to cast from
  // Example:
  // CollisionVolume = CreateDefaultSubobject<USphereComponent>(TEXT("CollisionVolume"));
  // CollisionVolume->SetupAttachment(RootComponent);
}

// In this example we'll implement the LineTrace inside the Tick
// but you can move the Code wherever you want!
void AActor::Tick(float Deltatime)
{
  Super::Tick(Deltatime);

  // Define the parameters for the LineTrace
  FHitResult Hit; // Stores the Hit-Result from the first blocking hit
  // Getting the Forward Vector, where the Actor is currently facing
  // and multipling it by 1000 units along the axis to define the End Point
  FVector Forward = GetActorLocation()->GetForwardVector() * 1000.f; 
  // Get the Actors Location which will be our Starting point for the Trace
  FVector Start = GetActorLocation();
  FVector End = Start + Forward; // Combine both to have the full LineTrace
  FCollisionQueryParams CollisionParams; // define the collision
  CollisionParams.AddIgnoredActor(this); // add Actors to ignore, in this case this Actor
  // if you want to ignore more Actors, just add the Line above again
  // and add a different Actor Type inside the parantheses

  if (Controller != nullptr) //check if the Controller is not null
    // Call the LineTrace Function and provide the parameters we just defined
    GetWorld()->LineTraceSingleByChannel(Hit, Start, End, ECC_Visibility, CollisionParams);
    // To vizualize the LineTrace in-game, lets also draw a DebugLine:
    DrawDebugLine(GetWorld(), Start, End, Hit.bBlockingHit ? FColor::Blue : FColor::Red, false, 5.0f, 1.0f, 10.0f);

    // Check if ImpactNormal and your Characters GetActorUpVector() are not null
    if (Hit.ImpactNormal && GetActorUpVector())
    {
      float DotProduct = FVector::DotProduct(Hit.ImpactNormal, GetActorUpVector());

      if (FMath::Abs(DotProduct) < ClimbableAngleThreshold)
      {
        // Wall is climbable.
        // if DotProduct is above 0, then its angled and if its 1 then the surface is vertical
      }
    }
    else
    {
      // if nothing got hit, you can do something here 
      // or just Log that nothing got got
      UE_LOG(LogTemp, Log, TEXT("Nothing got got"));
    }
}
```
<br />

The Logic is above at the last if-statement, but here again isolated:

**YourCharacter.cpp**

```cpp
// Check if ImpactNormal and your Characters GetActorUpVector() are not null
if (Hit.ImpactNormal && GetActorUpVector())
{
  float DotProduct = FVector::DotProduct(Hit.ImpactNormal, GetActorUpVector());

  if (FMath::Abs(DotProduct) < ClimbableAngleThreshold)
  {
    // Wall is climbable.
    // if DotProduct is above 0, then its angled and if its 1 then the surface is vertical
  }
}
else
{
  // if nothing got hit, you can do something here 
  // or just Log that nothing got got
  UE_LOG(LogTemp, Log, TEXT("Nothing got got"));
}

```
<br />