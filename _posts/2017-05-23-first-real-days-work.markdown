---
layout: post
title:  "Getting Started"
date:   2017-05-23 22:29:28 -0500
categories: update
---
So, today marks the first (real) day of development on Flint (the game we currently have no name for).  I've implemented some of the basics of 2d-action/adventure games.  Namely, dashing, and double-jumping.

Of the two, double-jumping was definitely the less involved.  In the header, I overrode the following builtin functions:

{% highlight cpp %}

// Jump function overrides
virtual void Jump() override;

virtual void StopJumping() override;

virtual void Landed(const FHitResult& Hit) override;

virtual bool CanJumpInternal_Implementation() const override;

{% endhighlight %}

And implemented them in the character like this:

{% highlight cpp %}

void AFlintCharacter::Jump()
{
	UE_LOG(LogTemp, Warning, TEXT("You tried to jump!"))
	bPressedJump = true;
	JumpKeyHoldTime = MaxJumpTime;
	CurrentJumpCount++;
}

void AFlintCharacter::StopJumping()
{
	bPressedJump = false;
	ResetJumpState();
}

bool AFlintCharacter::CanJumpInternal_Implementation() const
{
	UE_LOG(LogTemp, Warning, TEXT("Can we jump? %d %d"), CurrentJumpCount, MaxJumpCount)
	return Super::CanJumpInternal_Implementation() || (CurrentJumpCount < MaxJumpCount &&    CurrentJumpCount > 0);
}

void AFlintCharacter::Landed(const FHitResult& Hit)
{
	Super::Landed(Hit);
	CurrentJumpCount = 0;
}

{% endhighlight %}

Most of this code is pretty self explanatory.  The `Jump()` and `StopJumping()` functions are very similar to the base implementations, except we increment our `CurrentJumpCount` each time `Jump()` is called.

`CanJumpInternal_Implementation()` is an interesting function, and I was surprised to find out that, since I started working with UE4, had replaced the simpler implementation `CanJump()`.  Thankfully, the syntax is very simple, and we just add a condition to say "if the character hasn't jumped too much but has jumped some amount, let's let her jump again".  This is on top of whatever the super class returns.

The two interesting variables here are `MaxJumpCount` and `MaxJumpTime`.

I haven't implemented `MaxJumpTime` yet, but the plan is to have both double-jumping, and high-jumping for longer button presses (think Hollow Knight). For those of you less familiar with UE4, there are a number of predefined macros which allow us to describe to the engine how the interactive editor itself can interact with variables and functions.  So, the declaration of the two of these looks like this:

{% highlight cpp %}

// Maximum number of multi-jumps
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Movement")
int MaxJumpCount;

// Maximum jump time
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Movement")
float MaxJumpTime;

{% endhighlight %}

The `UPROPERTY` macro allows us to tell the editor how C++ variables can interact with the Blueprint scripting language in UE4.  In this case, I've given blueprints read/write access to both variables, to help speed up prototyping and balance testing as we develop the game.

That's it for this post.  In the next one, I'll talk about how I implemented dashing, and hopefully have another new feature to discuss.

Kyle

[Flint](https://kpitzen.github.io/Flint/)

[Blog Source](https://github.com/kpitzen/kpitzen.github.io)

