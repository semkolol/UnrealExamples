---
title: LineTraceSingleByChannel
slug: LineTraceSingleByChannel
docs: https://docs.unrealengine.com/5.2/en-US/API/Runtime/Engine/Engine/UWorld/LineTraceSingleByChannel/
---

<br />

_Description from [Documentation](https://docs.unrealengine.com/5.2/en-US/API/Runtime/Engine/Engine/UWorld/LineTraceSingleByChannel/):_

Trace a ray against the world using a specific channel and return the first blocking hit

<br />

Returns TRUE if a blocking hit is found

<br />

***

<br />

<br />

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

    // Check if something got hit and if the Actor is Valid
    // You can also check for specific Actors e.g.:
    // if (Hit.bBlockingHit && IsValid(Hit.GetActor()) && Hit.GetActor().IsA(AEnemy::StaticClass()))
    if (Hit.bBlockingHit && IsValid(Hit.GetActor()))
    {
      // Do something with the Actor here
      // Example:
      AEnemy* Enemy = Cast<AEnemy>(Hit.GetActor());
      if (Enemy)
      {
        Enemy->Health -= 10.f; // Damage
      }
    }
    else
    {
      // if no actor got hit, you can do something here 
      // or just Log that nothing got got
      UE_LOG(LogTemp, Log, TEXT("No Actor got got"));
    }
}
```
<br />

<br />
