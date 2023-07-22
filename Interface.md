---
title: Interface
slug: Interface
docs: https://docs.unrealengine.com/5.2/en-US/interfaces-in-unreal-engine/
---

<br />

_Description from [Documentation](https://docs.unrealengine.com/5.2/en-US/interfaces-in-unreal-engine/):_

Reference to creating and implementing interfaces.

<br />

***

<br />

*First:* Create a new C++ File in Unreal Engine and select "Unreal Interface" as a Parent Class. *Also:* If you want to make it OnOverlap based, dont forget to enable OverlapAllDynamics in your Details Pannel for your Aactors that Interact with eachother.
<br />

<br />

**YourInterface.h**

```cpp
#pragma once

#include "CoreMinimal.h"
#include "UObject/Interface.h"
#include "YourInterface.generated.h"

// This class does not need to be modified.
UINTERFACE(MinimalAPI)
class UYourInterface : public UInterface
{
	GENERATED_BODY()
};

/**
 * 
 */
class YOURGAME_API IYourInterface
{
	GENERATED_BODY()

	// Add interface functions to this class. This is the class that will be inherited to implement this interface.
public:
  // for this example were creating a Interact function
	virtual void Interact() = 0; // = 0; so we signal, to the compiler that there is no implementation of this function in the YourInterface.cpp
};

```

<br/>

**SomeActor.h**

```cpp
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "YourInterface.h" // include your Interface here
#include "SomeActor.generated.h"

UCLASS()
class YOURGAME_API ASomeActor : public AActor, public IYourInterface // add your Interface here
{
	GENERATED_BODY()

public:	
	ASomeActor();
	
	// Our Interact function from YourInterface.h
	virtual void Interact() override;
...
}
```

<br />

**SomeActor.cpp**

```cpp
#include "SomeActor.h"

ASomeActor::ASomeActor()
{
  //
}

// Called when the game starts or when spawned
void ASomeActor::BeginPlay()
{
	Super::BeginPlay();
}

void ASomeActor::Interact()
{
  // define what SomeActor should do on Interact
  UE_LOG(LogTemp, Warning, TEXT("You Successfully Interacted with SomeActor"));
}

```

<br />

**YourCharacter.h**

```cpp
#include "YourInterface.h" // add the Interface to your header file

```

<br />

**YourCharacter.cpp**

```cpp

AYourCharacter::AYourCharacter()
{
  //
}

// Called when the game starts or when spawned
void AYourCharacter::BeginPlay()
{
	Super::BeginPlay();
}

// For demonstration purposes: We'll just cast a LineTrace every Tick
// You obviously can set up your Inputs then cast a LineTrace from there
void AYourCharacter::Tick(float DeltaSeconds)
{
	Super::Tick(DeltaSeconds);

  // LineTrace Parameters
  FHitResult Hit;
  FVector Forward = FollowCamera->GetForwardVector() * 1000.f;
  FVector Start = GetCapsuleComponent()->GetComponentLocation();
  FVector End = Start + Forward;
  FCollisionQueryParams CollisionParams;
  CollisionParams.AddIgnoredActor(this);

  if (Controller != nullptr) // PlayerController is not null
    // add the parameters to the LineTrace
    GetWorld()->LineTraceSingleByChannel(Hit, Start, End, ECC_Visibility, CollisionParams);
    // Visualize the LineTrace with DrawDebugLine
    DrawDebugLine(GetWorld(), Start, End, Hit.bBlockingHit ? FColor::Blue : FColor::Red, false, 5.0f, 1.0f, 10.0f);

    if (Hit.bBlockingHit && IsValid(Hit.GetActor())) // chech if a Actor got got
    {
      // Cast YourInterface and check if its not null
      IYourInterface* Interface = Cast<IYourInterface>(Hit.GetActor());
      if (Interface)
      {
        // if Interface is not null call the Interact function
        Interface->Interact(); 
      }
    }
    else
    {
      // If the LineTrace does not hit an Actor
      UE_LOG(LogTemp, Log, TEXT("No Actor got got"));
    }
}

```

<br />