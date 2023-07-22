---
title: Attach something to a Socket
slug: attach-to-socket
docs: https://docs.unrealengine.com/5.2/en-US/skeletal-mesh-sockets-in-unreal-engine/
---

<br />

_Description from [Documentation](https://docs.unrealengine.com/5.2/en-US/skeletal-mesh-sockets-in-unreal-engine/):_

When attaching objects to Bones on your Skeletal Mesh, it may be necessary to offset this attachment. Instead of using math operations to estimate the offset transform, you can create Sockets. Sockets are dedicated attach points within the hierarchy of your Skeleton, which can be transformed relative to the Bone it is parented to. Once set up, you can attach your objects, weapons, and other actors to the Socket.

<br />

***

<br />

*First:* Create Sockets in your SkeletalMesh, and name them whatever you want. In this example we'll have a YourCharacter and a YourSword, just to show a usecase for this.
<br />

<br />

**YourCharacter.h**

```cpp
#pragma once

#include "CoreMinimal.h"
#include "Sword.h"
#include "YourCharacter.generated.h"

UCLASS(config=Game)
class AEmkoCharacter : public ACharacter
{
  GENERATED_BODY()


  UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = YourCharacter, meta = (AllowPrivateAccess = "true"))
    class USkeletalMeshComponent* CharacterMesh;

public:
  // Setup for the Actor that should be attached
  UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "YourGame", meta = (AllowPrivateAccess = "true"))
    class USkeletalMeshComponent* SwordMesh;

  // Define the Actor, that you want to attach
  // after you've done this and compiled, you have to select the Actor
  // in YourCharacter's Blueprint Details Pannel that you want to attach
  UPROPERTY(EditDefaultsOnly, BlueprintReadOnly, Category = "YourGame")
    TSubclassOf<ASword> Sword;

  // Reference to the spawned Actor, in this case the sword
  UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "YourGame", meta = (AllowPrivateAccess = "true"))
    ASword* SpawnedSword;
}
```

<br/>

**YourCharacter.cpp**

```cpp

YourCharacter::YourCharacter()
{
  //
}

// Called when the game starts or when spawned
void YourCharacter::BeginPlay()
{
  // Call the base class  
  Super::BeginPlay();

  ...

  // Spawn Actor at Socket
  CharacterMesh = GetMesh(); // get the character mesh

  // check if Sword is not null (has to be selected in YourCharacter's Blueprint Details Pannel)
  if (Sword)  
  {
    // Spawn the sword at Zero Location and Rotation
    SpawnedSword = GetWorld()->SpawnActor<ASword>(Sword, FVector::ZeroVector, FRotator::ZeroRotator);

    if (SpawnedSword && SwordMesh)
    {
      // Attach SwordMesh to CharacterMesh 
      SwordMesh->SetupAttachment(CharacterMesh);
      // Attach it to the Socket "sword", that what I named it in the Unreal Engine Editor - just enter you SocketName 
      SwordMesh->AttachToComponent(CharacterMesh, FAttachmentTransformRules::SnapToTargetIncludingScale, "sword"); // replace "sword" with your SocketName
    }
  }
}

```

<br />